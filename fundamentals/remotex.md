---
title: Execute Commands
permalink: fundamentals/remotex.html
type: page
layout: getstarted.tmpl
series: SaltStack Fundamentals
step: 3
overview:
  goals:
    - Remotely execute shell commands on managed systems
    - Remotely execute Salt execution modules on managed systems
  time: 5
  difficulty: 1
---

After you install SaltStack, you are immediately ready to run shell commands,
update packages, and distribute files to all managed systems simultaneously. As
a bonus, all responses are returned in a consistent, configurable format so you
can easily see what did and didn’t work.

10 minutes to measurable productivity? Not bad.

{: section sidebar :}

#### Command Syntax

Salt commands are executed using a common structure:

<img width="100%" src="{{ conf.images }}/cmd-syntax.png">

**target**: Determines which systems apply the command. Uses hostname globbing
by default, but there are many other ways to select and filter that we'll get
into later. For now, it is enough to know that \* targets all managed systems.

**command (module.function)**: This is how you leverage the real power of Salt.
Commands consist of a module and function, and Salt comes with built-in modules
to install software, copy files, check services, and most other tasks you want
to automate.

**arguments**: Provides any extra data needed by the function you are calling.
For example, the pkg.install function likes to know which packages you want to
install. You tell it using an argument.

#### Doc

You can pass a module or function name to the sys.doc execution module to get
details on any module directly from the command line. This list is filtered
based on the target(s).

``` bash
salt '*' sys.doc
```

``` bash
salt '*' sys.doc pkg
```

``` bash
salt '*' sys.doc pkg.install
```

{: end sidebar :}

#### Run a Shell Command

Salt lets you remotely execute shell commands across multiple systems using cmd.run:

``` bash
salt '*' cmd.run 'ls -l /etc'
```

All managed systems *simultaneously* and *immediately* execute this command and
then return the output to the Salt master. Feeling the power?

![]({{ conf.images }}/vagrant-cmd-run.png)

#### Salt Execution Functions

While shelling out using cmd.run is certainly useful, the real power comes when
you add Salt execution functions. The Salt community has put tremendous effort
into creating hundreds of functions that simplify most management tasks. Even
better, the same function can be used consistently across all supported
platforms.

Resist the urge to shell out. Learn the ways of the Salt execution functions.

#### Show Disk Usage

``` bash
salt '*' disk.usage
```

![]({{ conf.images }}/vagrant-disk.usage.png)

#### Install a Package

``` bash
salt '*' pkg.install cowsay
```

![]({{ conf.images }}/vagrant-pkg.install.png)

#### List network interfaces

``` bash
salt '*' network.interfaces
```

![]({{ conf.images }}/vagrant-network.interfaces.png)

These are just a few examples of the many execution functions available.

#### But who runs commands on *all* of their systems?

Good question! In the next section, we'll introduce the powerful targeting
mechanism that lets you run commands on specific groups of systems.

