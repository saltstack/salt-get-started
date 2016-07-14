---
title: Connect
permalink: ssh/connect.html
type: page
layout: getstarted.tmpl
series: Agentless Salt
step: 3
overview:
  goals:
    - Create a Salt roster file with connection details
    - Connect to a remote system and run a command
  time: 10
  difficulty: 2
---

Let's learn how Salt SSH stores connection details and connects to agentless systems. 

## Roster File

{: section sidebar :}

#### Extending the Roster

As is common in Salt, the roster system can be extended. You can currently use any of the following data sources to populate the Salt roster:

- [ansible](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.ansible.html)
- [cache](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.cache.html)
- [cloud](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.cloud.html)
- [clustershell](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.clustershell.html)
- [flat (default)](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.flat.html)
- [range (in development)](https://docs.saltstack.com/en/develop/ref/roster/all/salt.roster.range.html)
- [scan](https://docs.saltstack.com/en/latest/ref/roster/all/salt.roster.scan.html)

This guide uses the flat file roster.

{: end sidebar :}

By default, Salt uses a YAML file to store connection details for agentless systems, called the roster. A basic roster file contains an ID, a host, and a user:

```yaml
ID:
  host: [IP or DNS name] 
  user: [username]
```

The default location of the roster file is `/etc/salt/roster`. You can use the `--roster-file=` argument (example below) to specify a different path if you want to save it somewhere else.

There are many additional options available in rosters, see [How Rosters Work](https://docs.saltstack.com/en/develop/topics/ssh/roster.html#how-rosters-work) for more details.

## Command Basics

The `salt-ssh` command is used to connect to agentless systems. This command is very similar to the `salt` command:

```bash
salt-ssh [target] [command] [arguments]
```

The main difference is that any system specified in the target must be defined in the roster file, and targets can be matched using globs or regex only.

## Initial Connection

The first time you connect to a system, Salt SSH returns a message with the key fingerprint. You can examine this key fingerprint, and then connect using the `-i` option to accept the key.
```bash
salt-ssh -i '*' test.ping
Permission denied for host managed, do you want to deploy the salt-ssh key? (password required):
[Y/n] y
Password for vagrant@managed:
managed:
    True
```
Salt SSH generated a local SSH RSA key to use for authentication. The user password is required the first time to deploy the key, then subsequent connections work without providing the user password:

```bash
salt-ssh '*' disk.usage 
managed:
    ----------
    /:
        ----------
        1K-blocks:
            41251136
        available:
            38318832
        capacity:
            4%
        filesystem:
            /dev/sda1
        used:
            1196392
    /dev:
        ----------
        1K-blocks:
            245916
        available:
            245904
        capacity:
            1%
        filesystem:
            udev
        used:
            12
...
```

--------------

{: tutorial :}

## Tutorial - Create a Roster File and Connect

Let's add a roster file and then run a test command. Make sure you are in the Salt SSH directory that you created previously
(`~/salt-ssh`) and you have activated the virtual environment (`source venv/bin/activate`).

#. Create a `salt-ssh/roster` text file and save connection details for each agentless system:
```yaml
managed:
  host: 192.168.70.11
  user: vagrant
```
You can add as many systems as you like to this file using this format. The
example above is based on the vagrant demo systems from the previous
section, but you can easily substitute the values from your own environment.

#. Create a `salt-ssh/Saltfile` text file and add connection options:
```yaml
salt-ssh:
  roster_file: /home/vagrant/salt-ssh/roster # replace 'vagrant' with your username if needed
  config_dir: /home/vagrant/salt-ssh
  log_file: /home/vagrant/salt-ssh/log.txt  
```
A [Saltfile](https://docs.saltstack.com/en/latest/topics/ssh/#define-cli-options-with-saltfile) lets you specify command line options that you want included each time you run a command. 

#. Test the connection by running the following command:
```bash
salt-ssh -i '*' test.ping
```

If all goes well you'll get a response from each system.

{: end tutorial :}

