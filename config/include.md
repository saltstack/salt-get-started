---
title: Includes
permalink: /config/include.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 4
overview:
  goals:
    - Reuse states
    - Understand when to use includes
  time: 5
  difficulty: 1
---

{: section gs-sidebar :}

#### State Trees {.sidebar}

State files are organized into a directory tree, called the state tree,
in the `/srv/salt/` directory. Any new states that you create should be
placed in this directory. To change this location,
update the file_roots settings in `/etc/salt/master`.

{: end gs-sidebar :}

To keep your states modular and reusable, each configuration task
should be described only once in your state tree
([D.R.Y](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)).
If you need to use the same configuration task in multiple places,
you can use in include.

Using an include is simple. At the top of your state file (outside of
any ID), add an include using the following format:

~~~ yaml
include:
  - sls1 
  - sls2
~~~

Where `sls1` and `sls2` are the names of the SLS files that you want to
include. Note that you don't need to include the .sls extension. 

If the state file that you want to include is in a subdirectory in your state
tree, use a dot (**.**) as a directory separator:

~~~ yaml
include:
  - dir.sls1 
~~~

Included states are inserted at the top of the current file and are processed
first.

## Example

Remember this state from earlier in this tutorial?

{% raw %}
~~~ yaml
sync directory using lftp:
  cmd.run:
    - name: lftp -c "open -u {{ pillar['ftpusername'] }},{{ pillar['ftppassword'] }}
           -p 22 sftp://example.com;mirror -c -R /local /remote"
~~~
{% endraw %}

This state depends on the lftp executable, so it might be a good idea to create
another state to make sure that lftp is installed. We can then combine both
states using an include.

`srv/salt/lftp.sls`:

{% raw %}
~~~ yaml
install lftp:
  pkg.installed:
    - name: lftp
~~~
{% endraw %}

`srv/salt/dir-sync.sls`:

{% raw %}
~~~ yaml
include:
  - lftp

sync directory using lftp:
  cmd.run:
    - name: lftp -c "open -u {{ pillar['ftpusername'] }},{{ pillar['ftppassword'] }}
           -p 22 sftp://example.com;mirror -c -R /local /remote"
~~~
{% endraw %}

The advantage to using an include is that you can use the `lftp.sls`
state elsewhere to install lftp without syncing these directories. 

## Why not just include using the Top File?

Instead of having one state include another:

~~~ yaml
include:
  - sls1

install a useful package:
  pkg.installed:
    - name: cowsay
~~~

You could optionally put each in their own state file
and then include both using the top file:

~~~ yaml
base:
  'web*':
    - sls1
    - sls2
~~~

If a state always needs some other state, then using an include is a better
choice. If only some systems should receive both states, including both
states in the Top file gives you the flexibility to choose which systems
receive each state.

