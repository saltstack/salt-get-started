---
title: Install Salt SSH
permalink: /ssh/system.html
type: page
layout: getstarted.tmpl
series: Agentless Salt
step: 2
overview:
  goals:
    - Create a Salt roster file with connection details
    - Connect to a remote system and run a command
  time: 10
  difficulty: 1
---

Salt uses a utility called Salt SSH to connect to agentless systems. This utility can be installed on
a management server, a development workstation, or your laptop. The simplest way is to install using pip:

```bash
pip install salt-ssh
```

## Demo Systems

You can use any two systems to test Salt SSH. If you have [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed, you can use the following Vagrantfile to quickly set up a demo environment:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :salt do |salt_config|
    salt_config.vm.box = "ubuntu/trusty64"
    salt_config.vm.host_name = 'salt.local'
    salt_config.vm.network "private_network", ip: "192.168.70.10"
  end

  config.vm.define :managed do |managed_config|
    managed_config.vm.box = "ubuntu/trusty64"
    managed_config.vm.host_name = 'managed.local'
    managed_config.vm.network "private_network", ip: "192.168.70.11"
  end
end
```

This uses the same base image as the [Demo Environment](../fundamentals/) from the *SaltStack Fundamentals* guide, so if you have completed that guide this might save you some bandwidth. 

--------------

{: tutorial :}

## Tutorial: Install Salt SSH

To get you up and running quickly, `Tutorial` sections show you how to install Salt SSH into a Python virtual environment with all Salt configuration files being loaded from a single directory.

This is a useful configuration to install on a development system or on your laptop,
since you can easily load this virtual environment to quickly manage your
servers using Salt. From a terminal:

#. Run `which pip`. If pip is not installed: [pip](https://pip.pypa.io/en/stable/installing/#installing-with-get-pip-py).
#. Run `which virtualenv`. If virtualenv is not installed: `pip install virtualenv`
#. Create a directory for Salt SSH. For this tutorial we'll create a `salt-ssh` directory in our home directory. Create and `cd` to this directory.
#. Run the following commands to create a virtual environment and install Salt SSH:
```bash
virtualenv venv
source venv/bin/activate
pip install salt-ssh
```

This directory now contains a virtual environment that you can activate any time you want to use `salt-ssh`. Just
open a terminal to the `salt-ssh` directory and enter `source venv/bin/activate`.

{: end tutorial :}

