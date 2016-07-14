---
title: SaltStack Flexibility
type: page
layout: getstarted-intro.tmpl
permalink: flexibility.html
parent:
  title: Get Started
  url: index.html
---

# SaltStack Flexibility

{: section sidebar :}

#### We Love All of You

Or at least all of your management approaches. When enthusiasts talk
about the 'best' management approach, they are usually right about a
particular approach being the best for *their* needs. But what about
*your* needs?

SaltStack is focused on creating the best set of management tools
available anywhere, and then providing as many ways as we can think of
to let you use them. No agent? No server? No problem.

{: end sidebar :}

Every management tool and their Git forks claim to be flexible, but when
we say flexible at SaltStack, we mean *entire-management-approach
flexible*.

SaltStack can be implemented to follow the most popular systems
management models:

-   **Agent and Server**
-   **Agent-only**
-   **Server-only**
-   Any or all of the above in the same environment

### Agent and Server

The standard model for configuration management. A management server
sends commands and configurations to agents running on large numbers of
systems, and results are returned to the server.

### Agent-only

If you have avoided management tools because you don't want to set up
all of that "extra stuff", this is likely the model for you. Salt agents
can perform management tasks with or without the involvement of a
server.

The best thing about this model is that it is already built-in; if you
don't want to use a management server, you simply don't set one up. You
can move back and forth between this model and the Agent and Server
model, or use them both together.

### Server-only ([Agentless](ssh/))

Salt commands can be executed on remote systems using SSH instead of the
Salt agent. Configure all of your Web servers using the Salt agent, but
perform ad hoc management tasks on your database servers over SSH. It is
completely up to you.

### No Size Fits All

The initial sections of this Get Started Guide demonstrate SaltStack
using the Agent and Server model to keep things simple. If you are
planning on using a different approach though, don't let this get in
your way. The main difference between each management model is the
SaltStack executable that you use and how it is called. When it comes to
doing work, SaltStack capabilities are fundamentally the same across
each model.

Imperative *and* Declarative Configuration Management
-----------------------------------------------------

SaltStack supports both of the leading methodologies to define system
configurations. Follow a declarative methodology using SaltStack's
powerful requisites system, or go imperative with SaltStacks built-in
ordered execution.
