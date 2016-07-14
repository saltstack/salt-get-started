---
title: Watching Events
permalink: event/events.html
type: page
layout: getstarted.tmpl
series: Event-Driven Infrastructure
step: 2
overview:
  goals:
    - Understand the event system basics
    - Monitor events 
    - Understand event format and data 
  time: 10
  difficulty: 1
---

Salt's internal components communicate with each other by sending and listening
to events. In Salt, events are sent for about everything you could imagine:

- Salt minion connects
- Key is accepted or rejected
- Job is sent
- Job results are returned from a minion
- Presence heartbeats (turned off by default)

Even the Salt command-line interface uses the event system. After a command is
sent to the Salt master, the Salt CLI simply watches the event bus for job
returns from targeted minions.

## Watching Events

{: section sidebar :}

#### Salt Runners

Runners are modules that execute on the Salt master to perform supporting
tasks. Runners report job status, show connection status, display events, read
data from external APIs, query connected Salt minions, and more.

{: end sidebar :}

Salt provides a runner that displays events in real-time as they are received
on the Salt master. 

~~~ bash
salt-run state.event pretty=True
~~~

If you are using a [demo environment](../fundamentals/) your event bus is
probably quiet, so open another terminal and send a `salt '*' test.ping`
command, or restart the `salt-minion` service on one of your minions.

The following are a few events that are similar to what you might see on your
Salt master: 

~~~ bash
salt/job/20150923203228234305/new	{
    "_stamp": "2015-09-23T20:32:28.235712",
    "arg": [],
    "fun": "test.ping",
    "jid": "20150923203228234305",
    "minions": [
        "minion2"
    ],
    "tgt": "*",
    "tgt_type": "glob",
    "user": "sudo_vagrant"
}
~~~

~~~ bash
salt/job/20150923203228234305/ret/minion2	{
    "_stamp": "2015-09-23T20:32:28.291789",
    "cmd": "_return",
    "fun": "test.ping",
    "fun_args": [],
    "id": "minion2",
    "jid": "20150923203228234305",
    "retcode": 0,
    "return": true,
    "success": true
}
~~~

~~~ bash
salt/auth	{
    "_stamp": "2015-09-23T20:30:02.998305",
    "act": "pend",
    "id": "minion1",
    "pub": "-----BEGIN PUBLIC KEY-----\...\n-----END PUBLIC KEY-----\n",
    "result": true
}
~~~

## Event Format

Events are made up of two main parts: the tag which identifies the event that
was fired, and the data that contains details about the event.

### Event Tag

All salt events are prefixed with `salt/`, with additional levels based on the
type of event. For example, job events are prefixed with `salt/job/`. Each
event part is separated using `/` to provide simple namespacing. This is
identifier is called the event tag, and often contains specific details such as
a job ID or minion ID.

Along with the event tag, each event also includes specific event data.

### Event Data

Each event contains a `timestamp` and additional keys and values
that are unique to that specific event.

Later when we explore the reactor, you'll learn how to use the event tag and
data to customize the reaction.

