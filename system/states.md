---
title: States
permalink: system/states.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 5
---

# State System

*This tutorial describes how the state system works. For a tutorial, see [Configuration management](/en/getstarted/config/).*

The Salt state system came soon after remote execution, and as you might expect, it leverages the remote execution system heavily. Salt state modules are very similar to Salt remote execution modules, with one important difference. State modules contain logic to see if the system is *already in the correct state*. In fact, after this determination is made, the State module often simply calls the remote execution module to do the work.

- State module commands are designed to work cross-OS and cross-platform.

- All State module commands return results in a consistent data structure. This makes it easy to examine results or store them in a database.

- All targeted systems can run a job at the same time since they all receive the command simultaneously.

- Salt uses a library of hundreds of Python modules to perform configuration management, and you can easily add your own (or better yet, contribute them to the project!). Any application or service that can be accessed using Python, a shell command, or nearly any other interface can be configured using a state module in Salt.

## State System Flow

The primary difference between the state system and the remote execution system are the flow controls within the state system. Salt provides special commands that let you define dependencies, reuse code, branch on failure, control flow, and leverage variables and templates.

This is best illustrated using hands-on examples, which we'll leave to the [Configuration management](/en/getstarted/config/) guide.

