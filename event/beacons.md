---
title: Beacons
permalink: /event/beacons.html
type: page
layout: getstarted.tmpl
series: Event-Driven Infrastructure
step: 4
overview:
  goals:
    - Understand Salt beacons
    - Use a Salt beacon to monitor a file or system process
  time: 10
  difficulty: 2
---

So far we've learned how to monitor the event bus for Salt-related events, and
how to enable a few additional events. At this point you might be thinking
*"Since I've already set up a dynamic communication infrastructure that can
monitor and react to Salt events in real-time, it would sure be swell if
I could use that system to also monitor other things such as system logins,
disk usage, and my database service."* (OK, maybe not quite in those words).

Those words or not, you are in luck, since that is *exactly* what beacons do!

Beacons let you monitor and raise events for things that are not Salt-related.
The beacon system allows the minion to hook into a variety of system processes
and continually monitor these processes. When monitored activity occurs in
a system process, an event is sent on the Salt event bus. 

Salt beacons can currently monitor and send Salt events for many system
activities, including:

- file system changes
- system load
- service status
- shell activity, such as user login
- network and disk usage

## Enable a Beacon

Salt beacons do not require any changes to the system process that is being
monitored, everything is configured using Salt.

Beacons are typically enabled by placing a top-level `beacons` section in the
minion configuration file:

``` yaml
beacons:
  inotify:
    home/user/importantfile:
      mask:
        - modify
```

### Beacon Monitoring Interval

{: section gs-sidebar :}

#### Avoiding Event Loops

If a beacon reactor might make a change that would
re-trigger the beacon, add and set the `disable_during_state_run` argument to
`True`.

``` yaml
beacons:
  inotify:
    home/user/importantfile:
      mask:
        - modify
    disable_during_state_run: True 
```

As with most loop conditions, you'll probably find out the hard way when you need to add this.

{: end gs-sidebar :}

Beacons monitor on a 1-second interval by default. To set a different interval,
provide an interval argument to a beacon. The following beacons run on 5- and
10-second intervals:

``` bash
beacons:
  inotify:
    /etc/httpd/conf.d: {}
    /opt: {}
    interval: 5
  load:
    - 1m:
      - 0.0
      - 2.0
    - 5m:
      - 0.0
      - 1.5
    - 15m:
      - 0.1
      - 1.0
    - interval: 10
```

After a beacon is configured, it triggers events just like any other Salt process.

## Adding a Beacon

We are going to add a beacon that watches for changes to a file. To do this,
we'll use the
[inotify](https://docs.saltstack.com/en/latest/ref/beacons/all/salt.beacons.inotify.html)
beacon and the `python-pyinotify` package. Since this package isn't on our Salt
minions, first we'll use Salt to install it.

### Install pyinotify and start the event runner

Open a command prompt to the `salt-vagrant-demo` directory, and ssh into `master`:

``` bash
vagrant ssh master
```

Install the `python-pyinotify` package on `minion1`:

``` bash
sudo salt 'minion1' pkg.install python-pyinotify
```

You should see output similar to the following:

``` bash
vagrant@saltmaster:~$ sudo salt 'minion1' pkg.install python-pyinotify
minion1:
    ----------
    python-pyinotify:
        ----------
        new:
            0.9.4-1build1
        old:
    python2.7-pyinotify:
        ----------
        new:
            1
        old:
```

Since we'll be generating an event shortly, remain logged in to your Salt master and start the event runner:

``` bash
salt-run state.event pretty=True
```

Leave that running in the terminal and continue.

### Set up a beacon

Open another command prompt to the `salt-vagrant-demo` directory, and ssh into `minion1`:

``` bash
vagrant ssh minion1
```

On `minion1`, edit the `/etc/salt/minion` file and add the following content at the bottom:

``` yaml
beacons:
  inotify:
    /home/vagrant/importantfile:
      mask:
        - modify
```
Save the file, and then restart the Salt minion service:

``` bash
sudo service salt-minion restart
```
The beacon is now saved and enabled. Next, we can create the file that we are
going to monitor for changes. In the `/home/vagrant/` directory, create
`importantfile`:

``` bash
touch importantfile
echo "some content" > importantfile
``` 

Back on your Salt master, you should have picked up an event similar to the following:

``` bash
salt/beacon/minion1/inotify//home/vagrant/importantfile	{
    "_stamp": "2016-02-03T22:32:09.592113",
    "data": {
        "change": "IN_MODIFY",
        "id": "minion1",
        "path": "/home/vagrant/importantfile"
    },
    "tag": "salt/beacon/minion1/inotify//home/vagrant/importantfile"
}
```

Remember that the event runner must be started before the file is modified or
you won't see the event (the event will still be triggered, you just won't see
it).

Now that we've got a beacon working and are generating other Salt events, it's
time to do something with these events using the Salt reactor.

