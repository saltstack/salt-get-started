---
title: Event Reactor
permalink: /event/reactor.html
type: page
layout: getstarted.tmpl
series: Event-Driven Infrastructure
step: 5
overview:
  goals:
    - Configure Salt reactors
    - Understand reactor SLS file syntax
    - React to an event
  time: 10
  difficulty: 3
parent:
  title: Get Started
  url: ../index.html
---

Salt's Reactor system gives you the ability to trigger actions in response to
any event. Not only can you trigger actions when jobs and tasks complete, you
can trigger actions when services go down, users log in, files are changed, and
from anywhere that you can send a custom event (end of a build script, cron
job, the list goes on).

## Reactor Configuration

The reactor is configured by adding a top-level `reactor` section to
`/etc/salt/master` or to `/etc/salt/master.d/reactor.conf` (only one `reactor`
section is allowed, so choose only one of these locations).

The following is an example of a reactor configuration that matches minion start events,
cloud resource destroyed events, and a custom event string:

``` yaml
reactor:                            # Master config section "reactor"

  - 'salt/minion/*/start':          # Match tag "salt/minion/*/start"
    - /srv/reactor/start.sls        # Things to do when a minion starts
    - /srv/reactor/monitor.sls      # Other things to do

  - 'salt/cloud/*/destroyed':       # Globs can be used to match tags
    - /srv/reactor/destroy/*.sls    # Globs can be used to match file names

  - 'myco/custom/event/tag':        # React to custom event tags
    - salt://reactor/mycustom.sls   # Put reactor files under file_roots
```

Reactor configuration is intentionally simple; the only job of the reactor section
is to associate an event tag with a reactor SLS file to run. The reactor is
single-threaded, so the heavy-lifting should be done in the reactor SLS file
(Reactor SLS files are spun off in a separate process and can take as long as
needed). 

Reactor SLS files have the full power of YAML and Jinja, so you can perform
filtering and tests using the event tag and data. 

Let's add a reactor to our demo environment so we can see the system in action.
Open the `salt-vagrant-demo/saltstack/etc/master` file and add a reactor
section:

``` yaml
reactor:
    - 'my/custom/event/tag':
    - salt://reactor/customevent.sls
```
This tells the Salt master that anytime it sees an event that contains
`my/custom/event/tag`, call the `customevent.sls` file.

We'll create the `customevent.sls` file in the next section after we learn
about reactor SLS files.

For now, restart the `salt-master` service (`service salt-master restart`) to
pick up the change, and then continue.

## Reactor SLS Files

You are already familiar with state SLS files, and reactor SLS files have many
similarities. Both state and reactor SLS files are written using YAML and
Jinja, but since they have some syntax differences and are used for different
purposes, they should be kept in separate locations in your Salt fileserver
(for example, reactors can go in a `/srv/salt/reactors` directory).

Since reactor SLS files execute on the Salt master, it is useful to think of
them more as entry points into the `salt` and `salt-run` commands rather than
as entry points into the state system.

So what exactly can you do with Salt reactors? Let's go on to the next section
to find out.

## Types of Reactions

Reactors trigger one of the following systems:

* **Remote execution**: run an execution module on the targeted minions. This
is anything you would do by calling the `salt` command (including applying
a state or highstate).

* **Runners**: These are tasks you would start using `salt-run`. For example,
the
[HTTP](https://docs.saltstack.com/en/latest/ref/runners/all/salt.runners.http.html)
runner can trigger a webhook. 

* **Wheel**: Wheel commands manage your Salt environment, performing tasks such
as accepting keys and updating configuration settings. Some of these tasks are
exposed by the `salt-key` utility.

### Remote Execution

{: section gs-sidebar :}

#### Other target types

If you like to use something other than a minion ID glob to target, you can use
the `expr_form` argument:

``` yaml
clean_tmp:
  local.cmd.run:
    - tgt: 'os:Ubuntu'
    - expr_form: grain
    - arg:
      - rm -rf /tmp/*
```
{: end gs-sidebar :}

This interface is a direct connection to the Salt execution modules. If you
think about this in terms of how remote execution is triggered from the command
line using the `salt` command, you'll know that the `salt` command expects three
things:

* target
* function
* arguments

Remote execution in the Salt reactor requires the same information, in YAML format: 

``` yaml
<section id>:
  local.<function>:
    - tgt: <target>
    - arg:
        <arguments>
```

Note that the execution module function that you want to call is prefaced by
`local` for reasons that are not really important to us right now (or maybe
ever).

Let's look at a simple package installation that you might perform
from the command-line:

``` bash
salt 'myminion' pkg.install cowsay
```

In a reactor SLS, this would look something like the following:

``` yaml
install cowsay on myminion:
  local.pkg.install:
    - tgt: 'myminion'
    - arg:
      - cowsay
```

Getting the hang of this? Just remember that since reactor SLS files run on the
master and use execution modules, a minion target needs to be specified upon
which to run the execution module. This parameter is called `tgt`, meaning
“target”. Additionally, arguments to be passed to the module are also passed
using the `arg` or `kwarg` (not kwargs) parameter.

### Runner and Wheel Modules

Calling Runner modules and Wheel modules from the Reactor uses a more direct
syntax since the function is being executed locally instead of sending
a command to a remote system. There are no `arg` or `kwarg` parameters (unless
the Runner function or Wheel function accepts a parameter with either of those
names.)

``` yaml
clear_the_grains_cache_for_all_minions:
  runner.cache.clear_grains
```

``` yaml
spin_up_more_web_machines:
runner.cloud.profile:
- prof: centos_6
- instances:
  - web11       # These VM names would be generated via Jinja in a
  - web12       # real-world example.
```

Here is a wheel example that accepts a minion key automatically (in production you'll
want to add additional integrity checks so you don't accept rogue minions).

{% raw %}
``` yaml
accept_new_minion:
  wheel.key.accept:
    - match: {{ data['id'] }}
```
{% endraw %}

### More Examples

The following reactor SLS might be used to react to a `salt/cloud/*/created`
event:

{% raw %}
``` yaml
new vm alert:
  local.pagerduty.create_event:
    - tgt: minion
    - kwarg:
        description: "New VM {{ data['name'] }}"
        details: "New VM on {{ data['provider'] }}: {{ data['name'] }}"
        service_key: 1162ee51ed6e46239265c969729c48eb
        profile: my-pagerduty-account
```
{% endraw %}

Notice how we are using the event data structure to customize the event
message? Most events also include the minion ID as `data['id']` where it makes
sense, so you can use this to take action directly on the minion that triggered
the event. For example, in the install cowsay example above, `myminion` could
be replaced with `data['id']`.

If you set up your build system to trigger a custom event when a build
completes, you could notify a slack channel with the status: 

{% raw %}
``` yaml
spam slack:
  local.slack_notify.post_message
    - tgt: buildserver
    - kwarg:
        channel: "Development"
        api_key: peWcBiMOS9HrZG15peWcBiMOS9HrZG15"
        message: "Build {{ data['build_id'] }} finished with status: {{ data['status'] }}"
```
{% endraw %}

The `build_id` and `status` would be custom data that you insert when you trigger the event.

### States

The `state` execution module can be used within a reactor SLS file to apply
a state or to trigger a highstate. You can also use Jinja to substitute values
and perform testing and filtering:

{% raw %}
``` yaml
{% if data['id'] == 'mysql1' %}
highstate_run:
  local.state.highstate:
    - tgt: mysql1
{% endif %}
```
{% endraw %}

## Let's try it out

Now that you understand reactor SLS files, create the
`salt-vagrant-demo/saltstack/salt/reactor` directory, and then create a file
called `customevent.sls` in that directory. Next, add one of the reactor SLS
examples from this section, or create your own. Make sure you update the target
to target one of your systems, for example `minion1`.

From the command-line on your Salt master, trigger the following custom event:

``` bash
sudo salt-call event.send 'my/custom/event/tag' 
```

This event will be picked up by the reactor and will trigger the
`customevent.sls` file you created.

This guide has shown you some of the many ways you can use the Salt
reactor to automate your infrastructure, the sky is the limit!

