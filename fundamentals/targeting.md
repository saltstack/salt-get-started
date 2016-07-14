---
title: Targeting
permalink: fundamentals/targeting.html
type: page
layout: getstarted.tmpl
series: SaltStack Fundamentals
step: 4
overview:
  goals:
    - Define a target to select one or more systems.
    - Use system data to define a target.
    - Use custom, user-defined data to define a target.
  time: 10
  difficulty: 2
modals:
  - id: globbing-modal
    title: Globbing?
    message: |
      Globbing lets you match one or more Salt minion IDs using a wildcard character.
      Asterisk (**\***) matches one or more characters, and question mark (**?**) matches a single character. 
      
      This is useful if your Salt minion IDs are configured according to function, for example,
      web1, web2, db1, db2, and so on.
    button: Easy!
---

Targeting is how you select Salt minions when running commands, applying
configurations, and when doing almost anything else in SaltStack that involves
a Salt minion.

The simplest way to target is using the Salt minion ID. This value can be set
to anything you want in the minion config file, and can be specified using the
`-i` option in the bootstrap script.

``` bash
salt 'minion1' disk.usage
```

#### Globbing

<a id="globbing-modal" href="#">Globbing</a> on the Salt minion ID is supported
by simply including a wildcard in the target:

``` bash
salt 'minion*' disk.usage
```

{: section sidebar :}

#### Grains

Grains are static information SaltStack collects about the underlying managed
system. SaltStack collects grains for the operating system, domain name, IP
address, kernel, OS type, memory, and many other system properties.

You can add your own grains to a Salt minion by placing them in the
`/etc/salt/grains` file on the Salt master, or in the Salt minion configuration
file under the `grains` section. For example, many users add a custom grain
called `role` to each Salt minion to describe the function of the system.

You can use the `grains.ls` command to list all of the grains on a Salt minion:

<img class="imgcenter" width="100%" src="{{ conf.images }}/vagrant-grains.png">

Note that grain names are lowercase.

{: end sidebar :}

## So Many Ways to Target

In an ideal world, each system in your environment has a structured name that
tells you everything you need to know about the hardware, OS, and system role.
While we wait for that world, SaltStack provides a powerful targeting system to
help you find and filter systems based on static and custom data.

Targets can be based on Salt minion system information using the Grains system:

``` bash
salt -G 'os:Ubuntu' test.ping
```

Targets can be filtered by regular expression:

``` bash
salt -E 'minion[0-9]' test.ping
```

Targets can be explicitly specified in a list:

``` bash
salt -L 'minion1,minion2' test.ping
```

Or Multiple target types can be combined in one command:

``` bash
salt -C 'G@os:Ubuntu and minion* or S@192.168.50.*' test.ping
```

Don't worry too much about these targeting methods just yet. Just be confident
that the targeting mechanism has enough flexibility to find the systems that
you want to manage.

#### Got it, What's Next?

Hopefully you've got a good understanding of how execution modules are used
from the command line, and how to filter Salt minions using targets. The next
step is to define re-usable configuration templates, called states, so we can
make more awesomeness happen at once.

