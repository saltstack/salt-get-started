---
title: Plug-ins 
permalink: system/plugins.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 2
---

# Salt Plug-ins

Any discussion about the Salt approach would be incomplete without a discussion of plug-ins. Understanding
plug-ins and Salt's pluggable architecture is often the "[eureka](https://en.wikipedia.org/wiki/Eureka_(word))" moment that turns Salt investigators into Salt evangelists.

The basic explanation is this: Salt's core framework provides a high-speed communication and event bus. This
framework connects and authenticates your managed systems, and provides a way for these systems to send notifications.

On top of this core framework, the remaining features of Salt are exposed as a set of loosely coupled, pluggable subsystems.

## Pluggable Subsystems

Salt contains over 20 pluggable subsystems, but most users are interested in only the handful that are used to directly manage systems. The following table
contains a list of some of the more common subsystems in Salt:

Subsystem | | 
--|--
Authentication | Authorizes a user before running a job.
File server | Distributes files.
Secure data store | Makes user-defined variables and other data securely available.
State representation | Describes your infrastructure and system configurations.
Return formatter | Formats job results into a normalized data structure.
Result cache | Sends job results to long-term storage.
Remote execution | Runs a wide variety of tasks to install software, distribute files, and other things you need to do to manage systems.
Configuration | Configures targeted systems to match a desired state.


{: section sidebar :}

#### What does it mean to be "pluggable"?

Salt doesn't define a built-in way to perform the task for any subsystem, each subsystem simply delegates its work to a plug-in. Salt provides a default plug-in for each system, but changing the plug-in in most cases is as simple as updating a configuration file. This pluggability is what makes Salt so flexibile. 

{: end sidebar :}

To illustrate, the following diagram shows several of the common
subsystems along with the most common plug-ins for each subsystem.

<img width="50%" class="imgcenter" src="{{ conf.images }}/salt-subsystems.png">

This diagram shows only a handful of the available subsystems and plug-ins, but it should give you a sense of the general architecture of Salt.

## Subsystems during a Job Run

When a job runs, several Salt subsystems are invoked in order
to process the job. The following diagram shows the subsystem flow of a typical
state run or remote execution job:

<img width="80%" class="imgcenter" src="{{ conf.images }}/salt-subsystems-workflow.png">

{: section sidebar :}

#### Plug-ins? It sounds like you are talking about Salt Modules! 

In Salt, plug-ins are known by their Python name, *module*. Since each plug-in is a Python module,
most of the time they are simply called modules, or more accurately, Salt *subsystem* modules (Salt auth modules, Salt fileserver modules, and so on). 

As long as you understand that each Salt module
is a plug-in that extends one of the many subsystems in Salt, you'll understand this relationship well enough. 

{: end sidebar :}

At each step, the subsystem delegates its work to the
 configured plug-in. For example, the job returner plug-in in step 7 might be one of 30 plug-ins including MySQL, reddis,
 or not be configured at all (the job returner plug-in can also run directly on the managed system after step 4).

 At each step, there are many plug-ins available to perform a task, resulting in hundreds of possible Salt configurations and workflows.

## Flexibility

This flexibility makes Salt an extremely powerful and customizable tool,
but it also makes it a bit hard to answer standard questions when you
are learning about the tool.

For fun, let's take the "technically accurate" approach and answer a few common questions about Salt:

- *How do you start Salt jobs?* - From any interface that can call Python, a REST API, the command line, or by using Salt's built-in scheduler.

- *How does salt format results?* -  YAML, JSON, plain text, Python data
  structure, and several other formats, and you can change the format anytime using a single argument.
  
- *What format does Salt use for configuration declarations?* - Choose from one of 15 supported formats depending on your use case, and you also have your choice of templating languages. The format is specified on a per file basis, so you can use multiple formats simultaneously.

- *Where are results stored?* Anywhere you want, you have 30 options! 

Aside from making you very annoying in conversation, answering questions this way does say something about the Salt approach to management. You know your infrastructure, and in today's complex environments there isn't a single best way to do anything.

Don't let this worry you though, Salt comes out-of-the-box with excellent defaults that are used by most Salt users. The main point is that the flexibility is there when and if you need it.

## Salt Components

With your new knowledge of the pluggable subsystems in Salt, hopefully you are starting to understand that each Salt component is actually a pluggable subsystem in Salt, with corresponding plug-ins. Salt grains? Salt pillar? Salt runners? All pluggable subsystems that are easily extended.

## Virtual Modules

We've covered a lot here, but there is one additional thing we need to tackle regarding modules. Remember earlier when we explained how Salt abstracts the underlying details of the operating system? One way that Salt achieves this abstraction is by using virtual modules.

Some management tasks are performed so differently between operating systems, there is very little code that can be reused between them when writing a plug-in. 

For example, package management on Debian systems is done using an execution module called `aptpkg`. On Redhat, it is done using an execution module called `yumpkg` (for reasons that should be apparent). If you've used Salt at all though, you'd know that salt calls the `pkg` remote execution module for package management and it works across all OSs.

To enable this type of abstraction, Salt loads these types of modules using a virtual module name. The `aptpkg` module contains instructions that basically say "If you are a Debian system, load this module as `pkg`. Otherwise, don't load this!" Similar code is present in the `yumpkg` with a check for Redhat or CentOS. This way multiple modules can exist to perform the same task, but only one gets loaded with the virtual name.

Keep this in mind when you are reading module documentation, as you often need to read the documentation for the non-virtual module to understand the behavior. You can search for `__virtualname__` inside a Salt module to find out the name that Salt uses when loading that module. 

Still with me? Good. Let's move on and talk about how Salt communicates on the wire.
