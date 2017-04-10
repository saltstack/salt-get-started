---
title: Salt Approach
permalink: system/index.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 1
---

You can get a general understanding of how Salt works by seeing it in action. That said, there are many things going on under the hood that can be hard to notice as data flies past on the console. This section of the Get Started tutorial explains how Salt works, the Salt subsystems, and how Salt's modular architecture lets you use and extend Salt to manage your entire infrastructure.

Before we get into the specifics of the Salt components, it is helpful to understand a few things about the way Salt approaches infrastructure management.

## Real-time communication

All Salt minions receive commands simultaneously. This means that the time it takes to update 10 or 10,000 systems is quite similar, and queries to thousands of systems can be done in seconds. The Salt way to get information about your infrastructure is to query it in real time rather than rely on an (often outdated) database. 

(Note: Databases are great for storing job results, and Salt supports over 30 job results plug-ins including mysql, elasticsearch, syslog, carbon, and reddis. Salt also supports interfacing with many data stores to provide secure configuration data. When it comes to decisions that depend on the current system configuration though, Salt views a static database a bit like asking each person in the room a question by sending a separate text message and then writing the answer in a notebook.)

## No freeloaders!

Salt minions do their own work. Communication from the Salt master is a lightweight set of instructions that basically says “if you are a minion with these properties: run this command with these arguments.” Salt minions determine if they match the properties when the command is received. Each Salt minion already has all of the commands that it needs stored locally, so the command can be executed and the results quickly returned back to the Salt master. The Salt master doesn't do anything for a minion that it can do (often better) on its own.

## Salt loves to scale

Salt is designed for high-performance and scalability. Salt's communication system establishes a persistent data pipe between the Salt master and minions using ZeroMQ or raw TCP, giving Salt considerable performance advantages over competing solutions. Messages are efficiently serialized on the wire using MessagePack. Internally, Salt uses Python Tornado (implemented by some really smart developers) as an asynchronous networking library, and Salt is tuned using leading-edge approaches to multi-threading and concurrency.
 
It is not uncommon to meet users with over 10,000 minions on a single master in production, and there are known deployments with over 35,000 minions on a single Salt master! Salt has proven real-world speed and scalability.

## Normalize everything

Normalization is the key to Salt's cross-platform management capabilities. Salt commands and states run the same whether you are targeting Linux, Windows, MacOS, FreeBSD, Solaris, or AIX, are on physical hardware or in the cloud, or if you are targeting a container. Salt abstracts the details of each OS, hardware type, and system tools so you can get right to managing your infrastructure.

Everything also includes returns: Salt commands return results in a consistent data structure for easy consumption and storage.

## Manage everything

Salt runs nearly everywhere that Python runs. For devices that can't quite manage Python, you can use the proxy minion system. This means that the only requirement to be managed by Salt is to support *any* network protocol (even if you wrote the protocol yourself!). Salt commands are sent to the proxy minion, which translates the salt calls into the native protocol, and then sends them to the device. The return data from the device is parsed, and then placed into a data structure and returned.

## Automate everything

Salt's event-driven infrastructure not only lets you automate initial system configuration, it lets you automate as-you-go to scale, repair, and perform ongoing management. Salt users automate deployment and maintenance of complex distributed network applications and databases, custom applications, files, user accounts, standard packages, cloud resources, and much more.

## Programming Optional

You can use all of the salt capabilities without learning a programming language. Salt's remote execution capabilities are CLI commands, and the State system uses YAML to describe the desired configuration of a system. 

You can still take the "infrastructure as code" approach---Salt has many tools to support this including a powerful requisite system with imperative and declarative execution. Salt just saves you from needing to write Python or Ruby code to describe your infrastructure.

## Extensibility through Modular Systems

Some management tools consider themselves extensible because they can run an external script. In Salt, *everything* is extensible. As you'll learn in the next section, even the underlying communication protocol is interchangeable. Salt has over 20 extensible subsystems, which means that if Salt can do it, then you can customize how it is done.

Salt can quickly adopt new technologies and manage new applications as they are developed, and you'll never be stuck with someone else's decision about which is the best way to manage your infrastructure.

## Plug-ins

In the next section, we'll learn about Salt plug-ins and why they are so critical to the Salt approach. 
