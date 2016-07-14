---
title: SaltStack Speed
type: page
layout: getstarted-intro.tmpl
permalink: speed.html
parent:
  title: Get Started
  url: index.html
---

# SaltStack Speed

In SaltStack, speed isn't a byproduct, it is a design goal. SaltStack
was created as an extremely fast, lightweight communication bus to
provide the foundation for a remote execution engine. SaltStack now
provides orchestration, configuration management, event reactors, cloud
provisioning, and more, all built around the SaltStack high-speed
communication bus.

The Salt master establishes persistent connections to all managed
system, and uses lightweight protocols to enable real-time communication
across thousands of managed systems.

## Pluggable Architecture

SaltStack provides a pluggable architecture that lets you extend nearly
any part of the system. For example, you can easily add custom execution
modules, send job results to a new system, and use beacons to monitor
any part a managed system.

