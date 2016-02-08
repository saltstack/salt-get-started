---
title: Manage Files
permalink: /config/files.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 7
overview:
  goals:
    - Deliver files and folders to manages systems
    - Understand Salt file templates
  time: 10
  difficulty: 1
parent:
  title: Get Started
  url: ../index.html
---

{: section gs-sidebar :}

#### File Templates {.sidebar}

Salt lets you manage files using templates and variables. Take a look at the
[salt.states.file
docs](https://docs.saltstack.com/en/latest/ref/states/all/salt.states.file.html)
to learn more.

#### Git Fileserver

Many organizations store all of their configuration and resource files under
source control for change tracking. Salt lets you use a Git repo as the file
server instead of (or in addition to) the local file system on your Salt master
to greatly simplify this workflow.

For example, if your `http.conf` is sourced in a Git repo, each time a change
is committed all targeted systems would receive the change the next time the
`file.managed` state runs.

See the [Git Fileserver Backend
Walkthrough](https://docs.saltstack.com/en/latest/topics/tutorials/gitfs.html#git-fileserver-backend-walkthrough)
to learn how to set up your own Git repo as a Salt file server backend.

{: end gs-sidebar :}

Getting the correct files on a system is often more challenging than installing
the correct software. Salt has a built-in file server that can deliver files
and folders to managed systems. 

## salt://

You are familiar with placing state files in the `srv/salt` directory, but what
you might not know is that any other files and folders you place in this directory
are also available to your Salt minions. You can reference these files in states using
the `salt://` URL followed by the path to the file relative to the `srv/salt` directory.

## File.Managed 

This state function lets you manage a local file by specifying the source file
on the Salt master.

~~~ yaml
deploy the http.conf file:
  file.managed:
    - name: /etc/http/conf/http.conf
    - source: salt://apache/http.conf
~~~

Since the `source` path starts with `salt://`, we can determine that the source
for this file is `/srv/salt/apache/http.conf` on our Salt master.

Each time this state is applied, Salt makes sure that the local file matches
the version on the server. This helps prevent configuration drift and helps
make sure that applications are configured identically on different systems.

For example, if you want to distribute a global configuration file for `lftp`
that includes a custom setting that limits download rates, we can do this using
`file.managed`:

~~~ yaml
install network packages:
  pkg.installed:
    - pkgs:
      - rsync
      - lftp
      - curl

copy lftp config file:
  file.managed:
    - name: /etc/lftp.conf
    - source: salt://_tmpl_lftp.conf
~~~

Another option, since our configuration change is a single line, is to use
`file.append` to simply insert the new line we want to add:

~~~ yaml
install network packages:
  pkg.installed:
    - pkgs:
      - rsync
      - lftp
      - curl

update lftp conf:
  file.append:
    - name: /etc/lftp.conf
    - text: set net:limit-rate 100000:500000
~~~

## File.Recurse

This state function copies an entire directory.

~~~ yaml
copy some files to the web server:
  file.recurse:
    - name: /var/www
    - source: salt://apache/www
~~~

## Summary

You should now have a better understanding of SaltStack's configuration
management system, with enough knowledge to start writing your own states. If
you are looking for inspiration, the [SaltStack Formula
Repo](https://github.com/saltstack-formulas) contains many examples.

