---
title: System Data
permalink: system/data.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 7
---

# Data

This section discusses the two primary aspects of system data: getting data *about* your systems, and getting data *to* your systems. Salt provides two different subsystems to perform each of these tasks respectively: Salt grains and Salt pillar.

This section also explains a third component, called Salt mine, which is used to push data from a minion to a shared data store on the master that can be accessed by all minions.

## Grains

{: section sidebar :}

#### How are grains gathered?

If you've read the earlier section on plug-ins, you already know how grains work. The grains subsystem has several Salt grains modules (aka plug-ins) that run on the minion to populate
the grains dictionary.

Since grains are simpler than remote execution modules, each grain module contains the logic to gather grain data across OSs (instead of using multiple modules and `__virtualname__`).

{: end sidebar :}

Grains are used to get data about your systems. Grains are static information about the underlying operating system, memory, disks, and many other system properties.

Grains are gathered automatically when the minion starts and are refreshed periodically or by using a remote execution command.

So what can you do with grains? You can gather an inventory using the grains execution module, which lets you list all grains, find systems with a specific grain value, and so on.

Grains are also an integral part of the targeting system. Grains are used to target Salt states and Salt pillar data, which leads us directly to the next minion data subsystem: Salt pillar.


## Salt Pillar

Salt pillar is used to deliver data to your systems. 
Think about the different custom data needed when configuring even a simple system: user names, service URLs, preferred installation paths, ports, non-default application settings, and many others. Often these values are different for each system or system role (web, database, and so on).

Salt pillar lets you define these data values and then assign them to one or more minions using targets. The values can then be inserted into Salt states using variables.

Salt pillar data is encrypted using the targeted minion's public key and sent over a secure channel, so Salt pillar is also well-suited to distribute secure data such as passwords and ssh keys since it can be decrypted only by the targeted minion. Salt pillar data is never written to disk on the minion.

The default Salt pillar module defines pillar using a YAML file, though over 30 Salt pillar modules (aka plug-ins) are available to support a wide-variety of backends. Popular options include Mongo and Redis, which are both designed to store structured data. Many users stick with YAML files, but use a private git repo to manage and distribute the pillar data.

## Salt Mine

The Salt mine is used to share data values among Salt minions. For example, if you set up a shared database, you can configure the Salt minion that is running on the database server to push its IP address to the Salt mine automatically. This is a better approach than storing it in a Salt state or in Salt pillar where it needs to be manually updated.

Later when you are setting up a system that requires this value, you can access the Salt mine directly from a Salt state file.

The Salt mine is very easy to use, the biggest issue for many users is remembering to use it!

The rest of the essential subsystems are covered in other Get Started guides, so we'll wrap up by covering a few Python basics that will help you use Salt more effectively.

