---
title: Apply and Target States
permalink: /fundamentals/top.html
type: page
layout: getstarted.tmpl
series: SaltStack Fundamentals
step: 6
overview:
  goals:
    - Create a Top file to match systems with state files
  time: 15
  difficulty: 2
next:
  title: Continue to Configuration Management Get Started
  url: ../config/
modals:
  - id: highstate-modal
    title: Highstate?
    message: |
      A highstate causes all targeted minions to download the
      `/srv/salt/top.sls` file and find any matching targets. If a matching
      target is found, the minion applies all of the states listed under that
      target. Many users schedule highstate runs at regular intervals to ensure
      that systems remain in compliance.
    button: Got It!
---

{: section gs-sidebar :}

#### SaltStack Formulas Repo { .sidebar }

The Salt Community provides a vast repository of Formulas at
<https://github.com/saltstack-formulas>.

#### Batch Size

If you have a large number of connected minions, you might want to limit how
many systems are updated at once. You can do this using the `--batch-size`
option:

``` bash
salt --batch-size 10 '*' state.apply
```

{: end gs-sidebar :}

Let's review what we've learned so far:

-   How to run a single command from the command line on one or more Salt minions.
-   Group multiple commands and use new commands to define re-usable states.

Notice how the state we created in the previous section did not contain any
information about the Salt minions that should receive the configuration? Salt
states are generic by design, and describe only *how* a configuration should be
achieved.

In this section we'll learn about a different file, called the Top file, that
describes *where* states should be applied.

States and the Top file work together to create the core of SaltStack's
configuration management capability.

## Introducing the Top File

The Top file is used to apply multiple state files to your Salt minions during
a <a id="highstate-modal" href="#">highstate</a>. The states that are applied
to each system are determined by the targets that are specified in the Top
file.

## Create your own Top file

Before we create a Top file, it might be useful to take a moment to think about
what what *your* system configurations look like. Consider the different types
of systems you set up, and what is common and unique about each. Each system
can receive multiple configurations, so start with the most general
configurations and work your way down to the specifics.

For example, you might start with a simple description similar to the following:

<img class="imgcenter" src="../images/planning1.png">

In the SaltStack paradigm, the list itself would be the Top file, and each item
on the list would be a state. Targets are used within the Top file to define
which states are applied to each Salt minion.

The following example shows how our configurations might be translated to YAML
and represented in a Top file:

![](../images/planning.png)

When the Top file is evaluated, Salt minions execute all states that are
defined for any target that they match. For example, a system with a Salt
minion ID of atl-web4-prod would apply the vim, scripts, users, apache, python,
and django states.

Now that we understand the purpose of the Top file, lets go ahead and create
one. If you are using the Vagrant demo files you'll notice that there is
already a top.sls file in the saltstack/salt directory, so we'll just update that
one. Otherwise you can create a new file. Add the following to your top.sls
file:

``` bash
base:
  '*':
    - common
  'minion1':
    - nettools
```

Hopefully it is clear to you what will happen when this is applied, so let's
try it. On your Salt master, run the following command to apply the Top file:

``` bash
salt '*' state.apply
#calling state.apply with no arguments starts a highstate.
```

As you expected, minion1 and minion2 both applied the common state, and minion1
also applied the nettools state.

Using what you know about the targeting system, you now know how to create
state and run them on the correct Salt minions using the Top file.

### What's Next?

You should now have a basic understanding of the SaltStack remote execution
capabilities, how to create simple states, and how to apply these states to
managed systems.

If you are ready to keep going, continue to the [Configuration Management Get
Started Guide](../config/).

