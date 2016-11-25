---
title: Install SaltStack
permalink: fundamentals/install.html
type: page
layout: getstarted.tmpl
series: SaltStack Fundamentals
step: 2
overview:
  goals:
    - Install SaltStack
    - View and accept connections from managed systems
    - Send a test command to all managed systems
  time: 10
  difficulty: 1
modals:
  - id: install-modal
    title: Install SaltStack
    message: |
      #### On the Salt Master
      
      Run these commands on the system that you want to use as the central management point:
      
      <pre class="language-bash"><code>curl -L https://bootstrap.saltstack.com -o install_salt.sh<br>sudo sh install_salt.sh -M</code></pre>
          
      (note the **-M** option for "install Salt master")
      
      #### On each Salt Minion
      
      Run these commands on each system that you want to manage using Salt.
      
      <pre class="language-bash"><code>curl -L https://bootstrap.saltstack.com -o install_salt.sh<br>sudo sh install_salt.sh</code></pre>
     
      #### Salt Hostname
      
      Salt minions attempt to contact the Salt master using the `salt` hostname. If you don\'t have DNS in your environment, or if the Salt master hostname is something other than `salt`, you can add the IP address of your Salt master server to the `/etc/salt/minion` configuration file by changing the `master` setting.
      
      For example, we\'ve set the Salt master setting directly to the IP address we configured for the demo Vagrant files:
      <img class="imagecenter" src="../images/vagrant-master-setting.png">
      
      The bootstrap script supports passing the Salt master IP address directly using the **-A** option.
    button: Got It!
  - id: install-video-modal
    title: SaltStack Installation Demo
    className: modal90
    message: <video controls autoplay preload="auto" width="640"> <source src="../video/installation.mp4" type="video/mp4" /><source src="../video/installation.webm" type=\'video/webm;codecs="vp8, vorbis"\'/> <p class="vjs-no-js">To view this video please enable JavaScript, and consider upgrading to a web browser that <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a></p> </video>
    button: Close 
---

To install SaltStack, you’ll need a detailed network diagram with your complete
DNS mappings, an enterprise database server, and yeah, you don't need any of
that stuff. All you need is a way to install packages on your systems, and
about 10 minutes.

You can install SaltStack from a package manager, pip, directly from source, or
using a bootstrap script. SaltStack also provides dedicated tools to create
machines and install Salt on public and private clouds (salt-cloud and
salt-virt). We won't cover those tools here, but just know that as soon as you
have a system under management, SaltStack is used the same across physical,
cloud, and virtual resources.

#### Install

If you used the Vagrant project that is linked in the [previous
section](index.html), SaltStack is already installed and the connections to
each minion are already accepted. You can complete the tasks in the *Accept
Connections* below to verify that your minions are connected. 

If you are using a different environment, the simplest way to install SaltStack
is using the bootstrap script. This script installs SaltStack with a couple of
commands on most operating systems. <a id="install-modal" href="#">Show me how
to install using bootstrap!</a>

{: section sidebar :}

<span id="install-video-modal"><img class="imgcenter nolightbox" width="75%" src="{{ conf.images }}/install-video.png"></span>

#### Packages

If you cannot use bootstrap, or if you prefer packages, additional installation
methods are documented at <http://repo.saltstack.com>.

#### Terms

**Salt master**: the central management system.

**Salt minion**: a managed system.

#### Architecture 101

![]({{ conf.images }}/arch2.png)

{: end sidebar :}

## Accept Connections

Each connection between the Salt master and Salt minion is managed and secured
using cryptographic keys. After installation, each Salt minion sends its public
key to the Salt master, where it eagerly waits to be accepted. Salt minion keys
must be accepted before systems can receive commands from the Salt master.

At the command prompt, `cd` into the `vagrant-demo-master` directory and run the
following command to log in to your Salt master:

``` bash
vagrant ssh master
```


![]({{ conf.images }}/vagrant-ssh-master.png)

#### View all keys

On the Salt master, you can quickly view all Salt minion connections and view
whether the connection is accepted, rejected, or pending.

``` bash
sudo salt-key --list-all
```

#### Accept a specific key

Before a Salt minion can connect, you must accept its key.

``` bash
sudo salt-key --accept=<key>
```

#### Accept all keys

``` bash
sudo salt-key --accept-all
```

![]({{ conf.images }}/vagrant-keys.png)

## Send a Command

After you have accepted each key, send a command from your Salt master to
verify that your Salt minions are listening:

``` bash
sudo salt '*' test.ping
```

![]({{ conf.images }}/vagrant-test.png)

If you don't see all of your Salt minions respond, well that is what happens
when you set things up manually. You probably typed something wrong, didn't
accept some keys, or forgot to add a space somewhere (\#soml). After your Salt
minions are all responding, continue to the next section to put SaltStack to
work.

