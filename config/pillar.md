---
title: Pillar
permalink: config/pillar.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 3
overview:
  goals:
    - Create the pillar Top.sls file
    - Define and assign some pillar keys to your systems
  time: 15
  difficulty: 1
---

{: section sidebar :}

#### Salt Pillar Explained

Salt pillar is a system that lets you define secure data that are 'assigned' to
one or more minions using targets. Salt pillar data stores values such as ports,
file paths, configuration parameters, and passwords.

#### {% raw %}{{ }} ?{% endraw %}

Values within the curly braces are [Jinja2](http://jinja.pocoo.org/)
statements and expressions. We'll discuss Jinja2 later in this guide.

#### pillar.get

[pillar.get](https://docs.saltstack.com/en/2015.5/topics/pillar/index.html#pillar-get-function)
can also be used to retrieve pillar data in Salt states. This function is a bit
more complicated than accessing the pillar dictionary, but it has some
enhancements that make it a better choice after you get the hang of using
pillar. 

{: end sidebar :}

Salt pillar is an essential component to make Salt states re-usable, so we are going to take
a quick detour from writing Salt states to set it up.

## Salt Pillar Top File

Salt pillar uses a Top file to match Salt pillar data to Salt minions. This Top file is very
much like the Top file that is used to match Salt states to Salt minions.

Like Salt state functions, Salt pillar is best learned by example. Create the
`salt-vagrant-demo-master/saltstack/pillar` directory (mapped by Vagrant to /srv/pillar),
and then create a new file called `top.sls`. Add the following:

``` yaml
base:
  '*':
    - default
```

Next, create a file named `default.sls` in the same `pillar` directory and add the
following:

``` yaml
editor: vim
```

When Salt pillar data is refreshed, each Salt minion is matched against the targets
listed in the `top.sls` file. When a Salt minion matches a target, it receives all
of the Salt pillar SLS files defined in the list underneath that target. Simple,
right?

Since our `*` glob matches all Salt minions, each Salt minion receives `default` with
a pillar key of `editor` with a value of `vim`.

## Refresh Salt Pillar

We are going to use the Salt pillar value we just configured, so let's first refresh
Salt pillar data on all minions:

``` bash
salt '*' saltutil.refresh_pillar
```

## Salt Pillar in Salt States

Salt pillar keys are available in a dictionary in Salt states, so you could now update
the example state from the previous section to use this Salt pillar key:

{% raw %}
``` yaml
vim installed:
  pkg.installed:
    - name: {{ pillar['editor'] }}
```
{% endraw %}


Salt pillar data is secure, and you can use it to keep portions of your Salt state
secret. For example, you could add the following `key:value` pairs to Salt pillar:

``` yaml
ftpusername: me
ftppassword: oxfm4@8t5gglu^h^&
```

And then reference them in a Salt state:

{% raw %}
``` yaml
sync directory using lftp:
  cmd.run:
    - name: lftp -c "open -u {{ pillar['ftpusername'] }},{{ pillar['ftppassword'] }}
           -p 22 sftp://example.com;mirror -c -R /local /remote"
```
{% endraw %}

## Salt Pillar on the command line

For testing or for ad hoc management, you can pass Salt pillar values directly on
the command line. These values override any value that might be set in a Salt pillar
file.

``` bash
salt '*' state.apply ftpsync pillar='{"ftpusername": "test", "ftppassword": "0ydyfww3giq8"}'
```

## Salt Pillar can customize nearly any part of a Salt state

Hopefully it is becoming clear that you can use the Salt pillar targeting system to
customize nearly any value in your Salt state files.

For example, you could create `prodftp.sls` and `stageftp.sls` Salt pillar files,
insert different credentials into each, and then target different systems with
each file. The same `examples.sls` Salt state file is used for each system, but
different user names and passwords would be used based on the Salt pillar file
received. 

