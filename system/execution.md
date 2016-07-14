---
title: Remote Execution
permalink: system/execution.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 4
---

# Remote Execution

*This tutorial describes how the remote execution system works. For a tutorial on using remote execution, see [Execute Commands](/en/getstarted/fundamentals/remotex.html).*

Salt was designed from the beginning to be a remote execution tool, and remote execution is used heavily by the other subsystems in Salt. 

- Salt commands are designed to work cross-OS and cross-platform. A `salt '*' pkg.install git` command uses yum, apt, pacman, or Salt's windows package repository under the covers based on the target platform. One command, many systems.

- All Salt commands return results in a consistent data structure. This makes it easy to examine results or store them in a database.

- All targeted systems can run a job at the same time since they all receive the command simultaneously.

- Salt uses a library of hundreds of Python modules to perform remote management, and you can easily add your own (or better yet, contribute them to the project!). Any application or service that can be accessed using Python, a shell command, or nearly any other interface can be exposed as an execution module in Salt.

## Remote Execution

The remote execution system is accessed using the `salt` command-line interface. Let's take a simple command sent from the Salt interface, and trace it through the Salt system:

``` bash
salt '*' test.rand_sleep 120
```

Based on what we learned about the Salt communication system, here is what happens:

1. This command is sent over the publisher port to all connected minions.
2. Each minion examines the command, evaluates itself against the target, and decides whether or not it should run the command.
3. Targeted systems run the command and then return the results to the request server.

Let's dig in and take a closer look at what happens on the minion when the command runs. First, each command is spun off into a separate worker thread, so a Salt minion can handle multiple jobs at once.

But how does the Salt minion turn the `test.rand_sleep 120` command into action? Salt execution modules of course!

All of the commands that are used to manage systems are provided by Salt execution modules. These modules are what do the actual work in Salt, and are easy to use once you know a few things about how they work.

{: section sidebar :}

#### Code Dive!

If you enjoy a code dive (who doesn't!) take a look at the [`rand_sleep`](https://github.com/saltstack/salt/blob/develop/salt/modules/test.py#L139) function and see if you can find the relationship between the arguments sent on the command line and the arguments sent to the function (spoiler alert: they are exactly the same, even down to the name).

{: end sidebar :}

When the Salt minion receives a command, it simply finds the correct module (in this example, the `test` module), and then calls the corresponding function (`rand_sleep`) providing the supplied arguments (`120`). In this sense, you can think of Salt as a (crazy powerful) abstraction layer to Python functions.

<img width="50%" class="imgcenter" src="{{ conf.images }}/cmd-syntax2.png">

When using remote execution, you'll want to bookmark the [complete list of execution modules](https://docs.saltstack.com/en/latest/ref/modules/all/index.html). If you are ever wondering if Salt can manage a Tomcat server, this is your first stop (note: yes, it can). The module docs have many examples, and are simple to call.

Modules and functions are discussed again in the Python section, so let's move on for now and discuss the configuration management subsystem, Salt states.

