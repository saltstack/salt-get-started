---
title: Jinja
permalink: config/jinja.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 6
overview:
  goals:
    - Use Jinja conditionals
    - Use Jinja loops
    - Call salt execution modules using Jinja
  time: 15
  difficulty: 3
---

{: section sidebar :}

#### More Jinja2

Jinja2 is widely used, and there is a lot more detail in the [Jinja2
docs](http://jinja.pocoo.org/docs/dev/).

{: end sidebar :}

Salt includes the Jinja2 templating engine which can be used in Salt state files,
Salt pillar files, and other files managed by Salt.

Salt lets you use Jinja to access minion configuration values, grains and
Salt pillar data, and call Salt execution modules. This is in addition to the
standard control structures and Python data types that are already available in
Jinja.

## Conditionals

One of the most common uses of Jinja is to insert conditional statements into
Salt pillar files. 

Because many distros have different package names, you can use the `os` grain
to set platform specific paths, package names, and other values.

For example:

{% raw %}
``` yaml
{% if grains['os_family'] == 'RedHat' %}
apache: httpd
git: git
{% elif grains['os_family'] == 'Debian' %}
apache: apache2
git: git-core
{% endif %}
```
{% endraw %}

As you can see, Salt grains are available in a dictionary much like Salt pillar. This
example checks Salt grain values to set OS specific Salt pillar keys.

Save the snippet above to the saltstack/pillar/common.sls file, and then run
the following commands to refresh and then list Salt pillar values for each minions:

``` bash
salt '*' saltutil.refresh_pillar
salt '*' pillar.items
```
Your minions should list the values set for the `Debian` OS family.

After setting these values, when you apply the following Salt state:

{% raw %}
``` yaml
install apache:
  pkg.installed:
    - name: {{ pillar['apache'] }}
```
{% endraw %}

The `httpd` package is installed on RedHat, while the `apache2` package is
installed on Debian.

## Loops

Loops are useful for creating users and folders in Salt states.

{% raw %}
``` yaml
{% for usr in ['moe','larry','curly'] %}
{{ usr }}:
  user.present
{% endfor %}
```
{% endraw %}

{% raw %}
``` yaml
{% for DIR in ['/dir1','/dir2','/dir3'] %}
{{ DIR }}:
  file.directory:
    - user: root
    - group: root
    - mode: 774
{% endfor %}
```
{% endraw %}

In general, you should try to keep your Salt states as simple as possible. If you
find yourself writing complex Jinja, you should consider breaking a task into
multiple Salt states, or writing a custom Salt [execution
module](https://docs.saltstack.com/en/latest/ref/modules/index.html#modules-are-easy-to-write)
for the task (this is easier than it sounds, especially if you know some
Python!).

## Get Data using Salt

You can call Salt execution functions from Jinja to get real-time data from the
system.

{% raw %}
``` yaml
{{ salt.cmd.run('whoami') }}
```
{% endraw %}

