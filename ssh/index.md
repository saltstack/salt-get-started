---
title: Introduction
permalink: /ssh/index.html
type: page
layout: getstarted.tmpl
series: Agentless Salt
step: 1
---

{: section gs-sidebar :}

#### SSH {.sidebar}

All communication with the managed system when using Salt agentless is executed over SSH, which makes management substantially slower than standard Salt with ZeroMQ.

Though for the record, it is still much faster than logging in!

{: end gs-sidebar :}

You can use Salt agentless to run Salt commands on a system without installing a Salt minion.
The only requirements on the remote system are SSH and Python.

When running in agentless mode, Salt:

- Connects to the remote system over SSH.
- Deploys a thin version of Salt to a temp directory, including any required files.
- Runs the specified command(s).
- (Optional) Cleans up the temp directory.

You can use Salt agentless in conjunction with a master-minion environment, or you can manage all of your
systems agentless.

This Get Start guide walks you through the essentials of running Salt in agentless mode, and includes a tutorial that shows you how to create a standalone Salt SSH environment.

