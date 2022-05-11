---
title: Functions 
permalink: config/functions.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 2
overview:
  goals:
    - Call Salt state functions
    - Understand the difference between Salt state and Salt execution functions
  time: 15
  difficulty: 1
---

{: section sidebar :}

#### Terminology  

-|-
| Formula | A collection of Salt state and Salt pillar files that configure an application or system component. Most formulas are made up of several states spread across multiple state files.|
| State | A reusable declaration that configures a specific part of a system. Each state is defined using a state declaration.|
| State Declaration | A top level section of a state file that lists the state function calls and arguments that make up a state. Each state declaration starts with a unique ID.|
| State Functions | Commands that you call to perform a configuration task on a system.|
| State File | A file with an SLS extension that contains one or more state declarations.|
| Pillar File | A file with an SLS extension that defines custom variables and data for a system.|

#### Function Docs

The [Salt state function
docs](http://docs.saltstack.com/en/latest/salt-modindex.html#cap-s) are your
best resource to find available functions and arguments (Salt state functions start
with `salt.states.*`).

#### Vagrant Shared Folders

To simplify things, our Vagrant file maps the `/srv/salt` directory on your Salt
master to the local `salt-vagrant-demo-master/saltstack` directory. This means
that you can use a local text editor and save the file to the local file
system, and Vagrant makes it appear as if it were on the Salt master.

{: end sidebar :}

You learned the basics of the configuration management system in [SaltStack
Fundamentals](../fundamentals/), so let's take a deep-dive and
learn more about **Salt state functions**.

## Salt State Functions

Salt State functions are what do the work in your Salt states, and are the most important
thing to master when using SaltStack's configuration management system. 

Functions are available to install and configure applications, create users,
distribute files, and about anything else you might need to do when setting up
your systems.

Before we call any functions, let's go over how Salt states are represented 
and the syntax for Salt state function calls. 

## Syntax

Salt states are described using YAML, which is a simple language for describing
structured data (similar to JSON, but more human friendly).

The following diagram shows the format of a Salt state declaration:

![]({{ conf.images }}/state-file-described.png)

The first line in a Salt state declaration is the ID. Underneath the ID is where you call
one or more Salt state functions. 

-   The line with the ID and the line with each function call end with a colon
    (:). - Each function call is indented two spaces below the ID. - Parameters are
    passed as a list to each function. Each line that contains a function argument
    starts with a two space indentation, then a hyphen, then an additional space.
-   If an argument takes a single value, the name and value are on the same line
    separated by a colon and a space. - If an argument takes a list, the list
    starts on the next line and is indented two spaces.

## Function Examples

The best way to learn how to use Salt state functions is by examples. 
To see these examples in action, let's start the vagrant demo
environment you set up in [SaltStack Fundamentals](../fundamentals/).

Open a terminal to the `salt-vagrant-demo-master` directory and run `vagrant up`.
Refer back to [Demo
Environment](../fundamentals/)
section of SaltStack Fundamentals if you need a refresher or if you no longer
have the Vagrant environment.

While your environment is coming up, create
a file named `salt-vagrant-demo-master/saltstack/salt/examples.sls` that you can use
to run these examples. 

## Install a Package

Copy the following snippet to the `examples.sls` file you created:

``` yaml
install vim:
  pkg.installed:
    - name: vim
```

When your Vagrant environment is up (you are back at the command prompt), ssh
to your Salt master:

``` bash
vagrant ssh master
```

Apply `examples.sls` by running the following command:

``` bash
sudo salt 'minion1' state.apply examples
```

![]({{ conf.images }}/functions-1.png)


For extra credit, take a look at the [state.pkg
docs](http://docs.saltstack.com/en/latest/ref/states/all/salt.states.pkg.html)
and see if you can figure out which function to call to ensure that this
package is not on the system (hint: the function starts with 'rem' and ends in
'oved').

``` yaml
remove vim:
  pkg.removed:
    - name: vim
```

You can follow a similar process to test the other examples in this section.
You can replace the contents of `examples.sls` each time, or you
can create new SLS files for each example. If you use new files, make sure
you update the `state.apply` call to reference the new name.

``` bash
sudo salt '*' state.apply name-of-your-sls-file-with-no-extension
```

## Create a Directory

A basic example that creates a directory.

``` yaml
create my_new_directory:
 file.directory:
   - name: /opt/my_new_directory
   - user: root
   - group: root
   - mode: 755
```

This example uses only a few of the available `file.directory` options. Take
a look at the
[docs](http://docs.saltstack.com/en/latest/ref/states/all/salt.states.file.html#salt.states.file.directory)
and try adding a few more.


## Service Running

This state ensures that a service is running on the Salt minion:

``` yaml
Make sure the mysql service is running:
  service.running:
    - name: mysql
```

Each state declaration is identified by a state ID. state IDs must be unique,
and they can contain spaces and numbers. In the previous example, `Make sure
the mysql service is running` is a valid ID.

You can add multiple Salt state function calls under a single state ID:

``` yaml
Install mysql and make sure the mysql service is running:
  pkg.installed:
    - name: mysql-server
  service.running:
    - name: mysql
```

## Start a Service during Boot

Make sure you look over the complete parameter list when you call a function, since
there is often a lot of functionality available by simply passing additional arguments.
By passing the `enable: True` to `service.running`, SaltStack will make sure
the service starts each time the system boots.

``` yaml
Make sure the mysql service is running and enable it to start at boot:
  service.running:
    - name: mysql
    - enable: True
```

## Download a Git Repo

Every function takes a `name` parameter. If you do not define a value for name,
Salt uses the state ID for the value of name. In this example, the `name`
parameter is `https://github.com/saltstack/salt.git`:

``` yaml
https://github.com/saltstack/salt.git:
  git.latest:
    - rev: develop
    - target: /tmp/salt
```

Using the ID as the name is popular with experienced users because keystrokes.
You could also write this state like this:

``` yaml
Clone the SaltStack bootstrap script repo:
  pkg.installed: 
    - name: git # make sure git is installed first!
  git.latest:
    - name: https://github.com/saltstack/salt-bootstrap
    - rev: develop
    - target: /tmp/salt
```

We recommend using the second format and always defining the `name` argument.
The ID should describe what the state is doing, even though it might require
more typing. Following this format tends to make your states more consistent
and easier to reuse, maintain, and debug.

## Add a User

``` yaml
user account for pete:
  user.present:
    - name: pete
    - shell: /bin/bash
    - home: /home/pete
    - groups:
      - sudo
```

The `groups` argument is a list, so it is on the next line, indented two
spaces, and preceded by a dash.

## Add an entry to hosts

``` yaml
myserver in hosts file:
  host.present:
    - name: myserver
    - ip: 192.168.0.42
```

A nice example of platform normalization. This state can be applied to multiple
platforms even though hosts are configured in different ways (Salt works out
the details so you don't have to worry about it).

## Call an Execution Function

The [service.restart](http://docs.saltstack.com/en/latest/ref/modules/all/salt.modules.service.html#salt.modules.service.restart)
and other execution functions can be called in Salt states.

``` yaml
restart vsftpd:
  module.run:
    - name: service.restart
    - m_name: vsftpd  # m_name gets passed to the execution module as "name"
```

Execution functions (short for "remote execution functions") are the commands
that you call from the `salt` command line, and they start with `salt.module.*`. 

(Bit of trivia: Execution functions start with `module.*` since they were the
first and only modules available in the initial versions of Salt. If Salt is
ever rewritten we should probably start these with `execution.*` instead.)

#### A Bit More About Salt Execution and Salt State Functions

You might be wondering why you would use the service *execution* functions instead
of the service *state* functions, and why there seems to be so much overlap
between Salt state and Salt execution functions. 

If you compare the names of the salt *execution* functions, such as
`service.restart`, to similar salt *state* functions, such as
`service.running`, you might notice a small but important difference. Salt state
functions are designed to make only the changes necessary to apply
a configuration, and to not make changes otherwise. Salt execution functions run each
time they are called, which may or may not result in system changes.

When you call the
`service.running` state function, the function checks first to see if the
service is already running and only starts it if needed. 
When you call the `service.restart` execution function, the
service is always restarted.

In many cases the Salt *state* function wraps the Salt *execution* function, and
adds additional code to determine if the execution function needs to be called.

## test=True {.notransform}

Applying a Salt state can result in many changes on the target system. Salt state
functions provide a mechanism to display the changes that will be made during
a live run. The return information will show states that will be applied in
yellow and the result is reported as `None`.

``` bash
sudo salt 'minion1' state.apply examples test=True
```

## init.sls {.notransform}

If you target a directory during a `state.apply` or in the state Top file, salt
looks for an `init.sls` file in that directory and applies it.

## That is (sort of) all there is to it

You could go forward from this point and create a wide variety of very useful
Salt states with only Salt state function calls. Salt ships with hundreds of
functions that can manage all sorts of applications and services. 

The rest of the features in SaltStack's configuration management system let you
make your states more re-usable with variables and loops, branch on failure, and make your
states declarative using statements such as 'include' and 'require'.
Mastering these advanced concepts will help you get the most value out of
SaltStack's configuration management system.

We'll discuss these features in the remaining sections.

