---
title: Custom Events
permalink: event/custom.html
type: page
layout: getstarted.tmpl
series: Event-Driven Infrastructure
step: 3
overview:
  goals:
    - Enable additional Salt events
    - Send custom events
  time: 10
  difficulty: 1
---

In addition to the built-in events, you can enable some additional events in
the Salt system, as well as generate your own events.

{: section sidebar :}

#### Enabling Events

The additional events described here are enabled by changing settings in your
[Salt master
config](https://docs.saltstack.com/en/latest/ref/configuration/master.html).

{: end sidebar :}

## Presence Events

Enabling presence events causes the master to periodically look for actively
connected minions. Presence events are fired on the event bus on a regular
interval with a list of connected minions, as well as events with lists of
newly connected or disconnected minions.

~~~ yaml
presence_events: True
~~~

## Salt State Events

Enabling Salt state events causes the Salt minion to send progress events as each
function in a Salt state run completes execution.

~~~ yaml
state_events: True
~~~

## Fire an event when a state completes

If you have a long configuration job that includes several Salt states, you can fire
an event when each Salt state completes by adding the `fire_event` argument:

~~~ yaml
nano installed:
  pkg.installed:
    - name: nano
    - fire_event: True
~~~

Or if you'd rather, you can replace `True` with a custom event string that will
be appended to the event that is triggered:

~~~ yaml
nano installed:
  pkg.installed:
    - name: nano
    - fire_event: nano/installed
~~~

## Fire a custom event

You can also trigger events directly from the command line with a custom event tag and event data. Try
running the following command from one of your Salt minions:

~~~ bash
salt-call event.send /my/test/event '{"data": "my event test"}'
~~~
This results in an event similar to the following:

~~~ bash
/my/test/event	{
    "_stamp": "2016-02-05T18:24:47.001310",
    "cmd": "_minion_event",
    "data": {
        "__pub_fun": "event.send",
        "__pub_jid": "20160205182446924651",
        "__pub_pid": 1933,
        "__pub_tgt": "salt-call",
        "data": "my event test"
    },
    "id": "minion1",
    "tag": "/my/test/event"
}
~~~

