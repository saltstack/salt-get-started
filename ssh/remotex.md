---
title: Remote Execution
permalink: /ssh/remotex.html
type: page
layout: getstarted.tmpl
series: Agentless Salt
step: 4
overview:
  goals:
    - Remotely execute shell commands on agentless systems
    - Remotely execute Salt execution modules on agentless systems
  time: 5
  difficulty: 1
---

After your systems are added to the roster, you are immediately ready to run shell commands,
update packages, and distribute files to all targeted systems simultaneously. As
a bonus, all responses are returned in a consistent, configurable format so you
can easily see what did and didn't work.

#### Run a Shell Command

Salt lets you remotely execute shell commands across multiple systems using
`cmd.run`:

``` bash
salt-ssh '*' cmd.run 'ls -l /etc'
```

All targeted systems *simultaneously* and *immediately* execute this command and
then return the output to the Salt master. Feeling the power?

{: section gs-sidebar :}

#### Have We Met? { .sidebar }

If you completed the [SaltStack Fundamentals](../fundamentals/index.html)
guide, this section is likely familiar. We copied these commands directly from that guide,
replacing `salt` with `salt-ssh`. Any command you can run using Salt you can
run using Salt SSH.

{: end gs-sidebar :}


#### Salt Execution Functions

While shelling out using `cmd.run` is certainly useful, the real power comes
when you add Salt execution functions. The Salt community has put tremendous
effort into creating hundreds of functions that simplify most management tasks.
Even better, the same function can be used consistently across all supported
platforms.

Resist the urge to shell out. Learn the ways of the Salt execution functions.

#### Show Disk Usage

``` bash
salt-ssh '*' disk.usage
```

#### Install a Package

``` bash
salt-ssh '*' pkg.install cowsay
```

#### List network interfaces

``` bash
salt-ssh '*' network.interfaces
```

These are just a few examples of the many execution functions available.

--------------

{: tutorial :}

## Tutorial - Remote Execution

All of the remote execution commands in this section can be executed in your demo environment. These examples match all systems in your roster using the `*` glob, but you can substitute this with the ID you assigned in the `roster` file instead:

```bash
salt-ssh 'managed' network.interfaces
```

You can also pass a comma-delimited list of systems:

```bash
salt-ssh 'server1,server2,server3' network.interfaces
```

{: end tutorial :}


