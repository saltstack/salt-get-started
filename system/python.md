---
title: Python
permalink: system/python.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 8
next:
  title: Continue to SaltStack Fundamentals 
  url: fundamentals/
---

# Python

I know that we said earlier that you don't need to be able to write Python or other code to use Salt, which is true. However, it is useful in many circumstances to be able to *read* Python, or at least Python function documentation, which turns out is much easier than writing Python.

This section explains the Python basics that help you better understand how Salt works.

## Modules

{: section sidebar :}

#### Modules are used differently by each subsystem!

If you glanced at the salt source folder, you are probably feeling a bit overwhelmed by Python code about now. Fortunately, there are only two module types that you really need to interact with directly: execution modules (`salt.module`) and state modules (`salt.states`).

The modules that let you format job results (`salt.outputters`) are enabled using the `--out` argument when calling the `salt` command. Most of the other subsystem modules are enabled in the Salt master or minion configuration files, including auth, beacons, engines, fileserver, pillar, proxy, returners, and probably one or two others that I missed. And some you don't have to do anything with, such as grains, since they do their work automatically.

{: end sidebar :}

In Salt, each subsystem plug-in is a Python module. In Salt terms, you can think of a module as a group of functions (aka commands) that manage an application (mysql, docker), a system component (disk, file) or interact with an external system (gitfs).

Here is what you need to know:

- All of the modules are in the `salt` folder in the source. A separate folder exists for each subsystem, and each module is a separate file ending in `.py`.

- Modules are namespaced in the format salt.*subsystem*.*module*. This namespace is visible in the docs, so you can quickly tell which type of subystem module you are viewing. One point of confusion: execution modules start with `salt.module` since they were the first and only modules available in the initial versions of Salt (when time travel is invented we should probably start these with `salt.execution` instead).

- Modules contain as many or as few functions as are needed. The file execution module (`salt.modules.file`) has a lot of functions because file management is sort of a big deal. The uwsgi stats server execution module (`salt.modules.uwsgi`) has just one.

## Functions

If you understood the previous section on modules, functions are even easier. You can think of functions as the specific commands within a module that you can call to manage and configure systems. For example, `salt.modules.pkg.install`, `salt.modules.network.interfaces` and `salt.modules.user.add` are all common execution functions. Functions are the verbs of Salt, and you can usually figure out which function you want to call by opening the module docs and looking at the function names.

## Arguments

Arguments are a little more complex, especially if you are not familiar with keyword arguments, and argument syntax is where you'll usually stumble as you are getting started with Salt. 

Arguments are expressed differently between remote execution and states, so we'll cover each separately.

### Execution function arguments

Execution arguments are passed as additional values when calling Salt on the command line, either as just a value, or as `argument=value`. Required values are usually passed in a specific order as they are only separated by spaces, and optional arguments that have a default value are passed as `argument=value`.

For example, take a look at the function signature for `user.add` that appears in the Salt docs (`user` is a virtual module, so we are looking at the docs for `useradd`):

```python
salt.modules.useradd.add(name, uid=None, gid=None, groups=None, home=None,
shell=None, unique=True, system=False, fullname='', roomnumber='',
workphone='', homephone='', createhome=True, loginclass=None)
```

When calling this module, name is required and you can see that there is not a default value provided. The remaining arguments will use the value listed unless you pass a different value as an override. The following example creates a user named Fred and sets a different default shell:

```bash
salt '*' user.add fred shell=/bin/zsh
```

Escaping follows the rules of your shell.

#### More Examples

Regular arguments and keyword arguments (which are usually shortened to args and kwargs) are the most common types you'll encounter. Here is an example of passing one arg and three kwargs:

```bash
salt '*' network.connect google-public-dns-a.google.com port=53 proto=udp timeout=3
```

Passing two args and one kwarg:

```bash
salt '*' cp.get_file salt://vimrc /etc/vimrc gzip=5
```

A few execution modules accept lists, which are passed as follows:

```bash
`salt ns1 pkg.install pkgs=['bind9','bind9-docs','bind-utils']`
```

Some even take a list of dictionaries:

```bash
`salt '*' pkg.install sources='[{"foo": "salt://foo.deb"},{"bar": "salt://bar.deb"}]'`
```

The Salt docs contain a lot of examples, and if you get stuck there are usually friendly Salt users in [IRC](http://webchat.freenode.net/?channels=salt&uio=mj10cnvljjk9dhj1zsyxmd10cnvl83) who can help you out.

### State function arguments

{: section sidebar :}

#### user.add vs user.present

Since state functions are stateful, the `salt.states.user.present` function checks first to see if the user already exists, and if not, creates the user to satisfy the state of "present". The `salt.modules.user.add` execution function simply adds the user, which will probably cause an error if the user already exists. 

{: end sidebar :}

State functions are called in state files using YAML syntax. The YAML syntax works great to represent data types, so you'll probably find that calling functions with complex data types is easier in states. To see how a user is added in a state, lets look at the `salt.states.user.present` function:

```bash
salt.states.user.present(name, uid=None, gid=None, gid_from_name=False,
groups=None, optional_groups=None, remove_groups=True, home=None,
createhome=True, password=None, hash_password=False, enforce_password=True,
empty_password=False, shell=None, unique=True, system=False, fullname=None,
roomnumber=None, workphone=None, homephone=None, loginclass=None, date=None,
mindays=None, maxdays=None, inactdays=None, warndays=None, expire=None,
win_homedrive=None, win_profile=None, win_logonscript=None,
win_description=None)
```

Even though there are a lot of arguments, calling functions with keyword arguments in YAML is easy:

```yaml
a state example that calls user.present:
  user.present:
    - name: fred
    - shell: /bin/zsh
```

Lists are also easy to define using YAML:

```yaml
install bind packages:
  pkg.installed:
    - pkgs:
      - bind9
      - bind9-docs
      - bind-utils
```

Defining a list of dictionaries is also easier than on the command line:

```yaml
Install some packages using other sources:
  pkg.installed:
    - name: mypkgs
    - sources:
      - foo: salt://foo.deb
      - bar: http://somesite.org/bar.deb
```

One more tip that applies to both execution and state functions: not all available arguments are listed in the function signature. Several execution functions act as wrappers to other Python APIs or interfaces, so they just pass along any arguments that you provide. These additional arguments are usually documented below the signature, but sometimes you'll have to check the code or the corresponding interface to know exactly what is supported.

This should give you enough understanding of how Python is used by the execution and state subsystems to get you started. Continue to *SaltStack Fundamentals* to learn how to install and start using Salt.
