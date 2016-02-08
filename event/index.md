---
title: Event-Driven Infrastructure
permalink: /event/index.html
type: page
layout: getstarted.tmpl
series: Event-Driven Infrastructure
step: 1
---

{: section gs-sidebar :}

#### What can I do with events? {.sidebar}

Here are just a few of the things our users do with Salt's event driven infrastructure:

- Send a text message when a user logs on to a production server.
- Notify a Slack or HipChat channel when a build fails.
- Instantly restore a configuration file when an unauthorized change is made.
- Automatically scale cloud resources.
- Monitor disk, processor, ram, and other system stats and take action outside of defined thresholds.

{: end gs-sidebar :}

If you completed the previous guides, you've learned how to use Salt to execute commands on
remote systems, and how to define reusable templates to configure systems.

Most configuration tools stop here (if they even make it this far!) but with Salt,
these capabilities are only half the story.

Salt is built around an event infrastructure that can be leveraged
and extended to drive reactive provisioning, configuration, and management across all
devices in your infrastructure.

This Getting Started guide introduces you to the Salt event system, and shows
you how to:

- Monitor and enable Salt events
- Enable monitoring for non-Salt activities
- Trigger reactions based on events

