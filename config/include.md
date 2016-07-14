---
title: Includes
permalink: config/include.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 4
overview:
  goals:
    - Reuse Salt states
    - Understand when to use includes
  time: 5
  difficulty: 1
---

{: section sidebar :}

#### Salt State Trees

Salt state files are organized into a directory tree, called the Salt state tree,
in the `/srv/salt/` directory. Any new states that you create should be
placed in this directory. To change this location,
update the file_roots settings in `/etc/salt/master`.

{: end sidebar :}

To keep your Salt states modular and reusable, each configuration task
should be described only once in your Salt state tree
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

If the Salt state file that you want to include is in a subdirectory in your Salt state
tree, use a dot (**.**) as a directory separator:

~~~ yaml
include:
  - dir.sls1 
~~~

Included Salt states are inserted at the top of the current file and are processed
first.

## Example

Remember this Salt state from earlier in this guide?

{% raw %}
~~~ yaml
sync directory using lftp:
  cmd.run:
    - name: lftp -c "open -u {{ pillar['ftpusername'] }},{{ pillar['ftppassword'] }}
           -p 22 sftp://example.com;mirror -c -R /local /remote"
~~~
{% endraw %}

This Salt state depends on the lftp executable, so it might be a good idea to create
another Salt state to make sure that lftp is installed. We can then combine both
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
Salt state elsewhere to install lftp without syncing these directories. 

## Why not just include using the Top File?

Instead of having one Salt state include another:

~~~ yaml
include:
  - sls1

install a useful package:
  pkg.installed:
    - name: cowsay
~~~

You could optionally put each in their own Salt state file
and then include both using the top file:

~~~ yaml
base:
  'web*':
    - sls1
    - sls2
~~~

If a Salt state always needs some other state, then using an include is a better
choice. If only some systems should receive both Salt states, including both
states in the Top file gives you the flexibility to choose which systems
receive each.

