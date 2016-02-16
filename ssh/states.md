---
title: Apply States 
permalink: /ssh/states.html
type: page
layout: getstarted.tmpl
series: Agentless Salt
step: 4
overview:
  goals:
    - Apply a Salt state using Salt SSH
  time: 10
  difficulty: 2
parent:
  title: Get Started
  url: ../index.html
---

Salt states are fully compatible with Salt SSH, and can be used to quickly
automate system setup and configuration. 

The [Configuration Management](../config/index.html) guide contains a detailed
explanation of creating Salt states. All of the information in that guide
(including pillar and Salt's file server) can be used to apply states using
Salt SSH.

--------------

{: tutorial :}

## Tutorial: States

Before we create and apply states, let's make a few additions to our Salt SSH
environment to configure the state system file location to point to a directory in our `salt-ssh` directory (by
default state files are stored at `/srv/salt`).

Make sure you are in the Salt SSH directory you created previously (`~/salt-ssh`)
and you have activated the virtual environment (`source venv/bin/activate`).

#. Create a `salt-ssh/master` file with the following content:
```yaml
    file_roots:
      base:
        - /home/vagrant/salt-ssh/states # you might need to replace 'vagrant' in your environment
```

#. Create a directory named `states`, and then add and save the following as `states/network.sls`:
```yaml
    install network packages:
      pkg.installed:
        - pkgs:
          - rsync
          - lftp
          - curl
```

#. Since we are installing packages, we'll need to enable elevated privileges before we apply this state.
Open the `roster` file and add `sudo: True`.
```yaml
managed:
  host: 192.168.70.11
  user: vagrant
  sudo: True
```

#. Apply the state using the following command:
```bash
salt-ssh '*' state.apply network
```
(Note that we previously added the `config_dir` path to our `Saltfile`, that is why `salt-ssh` finds  `salt-ssh/master` file.)

### Manage Files

Salt SSH states can also manage files! Salt SSH resolves all `salt://` file references
and sends the files to the agentless system automatically.

#. In the `salt-ssh/states` directory, create a subdirectory named `files`.

#. Add a `salt-ssh/states/files/sample.conf` file with the following:
```yaml
setting=value
setting2=value2
```

#. Add and save a new state file named `salt-ssh/states/config.sls` with the following:
```yaml
    manage some app config file:
      file.managed:
        - name: /tmp/sample.conf
        - source: salt://files/sample.conf
```

#. Apply the state using the following command:
```bash
salt-ssh '*' state.apply config
```

Each time you apply this state, the config file on the agentless system will
be updated to match the version in `salt-ssh/states/files`.

For some added fun, you can list the contents of the config file using remote execution:

```bash
salt-ssh '*' cmd.run 'cat /tmp/sample.conf'
```

### Source Control

Instead of placing state files in the `salt-ssh/states` directory, Salt can be
easily configured to access a GitHub repo by changing the contents of
`salt-ssh/master` to the following:

```yaml
fileserver_backend:
  - git

gitfs_remotes:
  - https://github.com/yourusername/yourrepo.git
```

See [Git Fileserver Backend](https://docs.saltstack.com/en/latest/topics/tutorials/gitfs.html) for more details.

### More Examples

Here are just a few examples of other commands you can add to a state file to
configure your systems.

**Clone from Github**

```yaml
Clone the SaltStack bootstrap script repo:
  pkg.installed: 
    - name: git # make sure git is installed first!
  git.latest:
    - name: https://github.com/saltstack/salt-bootstrap
    - rev: develop
    - target: /tmp/salt
```

**Service Running**

```yaml
Make sure the mysql service is running:
  service.running:
    - name: mysql
```

**Create Users**

```yaml
user account for pete:
  user.present:
    - name: pete
    - shell: /bin/bash
    - home: /home/pete
    - groups:
      - sudo
```

This guide has shown you how simple it is to start managing servers using Salt SSH. All of the state files and commands you use with Salt SSH are fully compatible with Salt running in master-minion mode, so you can easily switch between the two modes.

{: end tutorial :}

