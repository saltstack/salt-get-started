---
title: Runners
permalink: system/runners.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 6
---

# Salt Runners

The runner subsystem provides Salt modules that execute **on the Salt master** (remember that execution and state modules run simultaneously and independently on each targeted minion, not on the Salt master). 

Runners are available to list job status, view events in real-time, manage Salt's fileserver, view Salt mine data, send wake-on-lan to minions, call webhooks and make other http requests, and much more.

- Runners are called using the `salt-run` command line interface. They do not take a target because the target is the Salt master where you execute the command.

- The syntax for passing arguments to Salt runners is identical to the syntax used to pass arguments to Salt execution modules.

## Orchestrate Runner

The orchestrate runner gets its own section since it provides one of Salt's core capabilities: the ability to run commands and apply configuration across multiple minions in a defined order. For example, using the orchestrate runner you can configure multiple systems using the full power of the state system (includes, requisites, branch on fail, dependencies, ordered execution, and so on). This is covered in-depth in the [Orchestration with Salt](https://docs.saltstack.com/en/latest/topics/tutorials/states_pt5.html) tutorial when you are ready to explore this feature.

