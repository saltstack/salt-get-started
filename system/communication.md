---
title: Communication
permalink: system/communication.html
type: page
layout: getstarted.tmpl
series: Understanding SaltStack
step: 3
---

# Communication & Security

This section describes the Salt communication model along with a basic overview of authentication and security.

## Architecture Model

Salt uses a server-agent communication model, (though it works well as a standalone single-server management utility, and also provides the ability to run agentless over SSH). The server component is called the Salt master, and the agent is called the Salt minion.

<img width="30%" class="imgcenter" src="{{ conf.images }}/master-minion.png">

The Salt master is responsible for sending commands to Salt minions, and then aggregating and displaying the results of those commands. A single Salt master can manage thousands of systems.

## Communication Model

Salt communicates with managed systems using a [publish-subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) pattern. Connections are initiated by the Salt minion, which means that you do not need to open any incoming ports on those systems (thereby reducing the attack vector). The Salt master uses ports 4505 and 4506, which must be opened to accept incoming connections.

<img height="30px" class="imgcenter" src="{{ conf.images }}/basic-comm.png">

-|-
|**Publisher**|(port 4505) All Salt minions establish a persistent connection to the publisher port where they listen for messages. Commands are sent asynchronously to all connections over this port, which enables commands to be executed over large numbers of systems simultaneously.|
|**Request Server**|(port 4506) Salt minions connect to the request server as needed to send results to the Salt master, and to securely request files and minion-specific data values (called Salt pillar). Connections to this port are 1:1 between the Salt master and Salt minion (not asynchronous).|

## Salt Minion Authentication

When the minion starts for the first time, it searches the network for a system named `salt` (though this can be easily changed to an IP or different hostname). When found, the minion initiates a handshake and then sends its public key to the Salt master.

<img width="30%" class="imgcenter" src="{{ conf.images }}/public-key.png">

After this initial connection, the Salt minion's public key is stored on the server, and it must be accepted on the Salt master using the `salt-key` command (or through some automated mechanism). This can be a source of confusion for new users since Salt won't provide the security key required to decode messages until the Salt minion's public key is accepted (which means that the Salt minion won't run any commands until its key is accepted).

After the minion key is accepted, the Salt master returns its public key along with a rotating AES key that is used to encrypt and decrypt messages that are sent by the Salt master. The returned AES key is encrypted using the public key initially sent by the Salt minion, and can therefore be decrypted only by that Salt minion.

## Secure Communication

All further communication between the Salt master and the Salt minion is encrypted using AES keys. The AES encryption key uses an explicit initialization vector and a CBC block-chaining algorithm in accordance with the latest accepted version of TLS.

## Rotating Security Keys

A rotating AES key is used to encrypt jobs that are sent to the Salt minion by the Salt master, and to encrypt connections to the Salt master fileserver. A new key is generated and used each time the Salt master restarts and each time a Salt minion key is deleted using the salt-key command. 

After the key is rotated, all Salt minions must re-authenticate to receive the updated key. This enables the AES key to rotate without interrupting the minion connection.

## Encrypted Communication Channels

Publication communication between the Salt master and Salt minion is encrypted using a rotating AES key. Direct communication between the Salt master and Salt minion is encrypted using a unique AES key for each session. 

For example, published jobs are encrypted using the rotating AES key, while secure, minion-specific data sent using Salt pillar is encrypted with a unique AES key for each Salt minion and session.

## User Access Control

Before a command is sent to minions, Salt performs some internal checks against the Publisher ACL to make sure that the user executing the command has the correct privileges. If the user is authorized to run the specified command against the specified target, the command is sent. If not, an error is returned. Salt also returns a list of the minions that are expected to respond to the command line interface, so it knows how long to wait for returns.

Next, we'll go over the main pluggable subsystems that you'll use to manage infrastructure, starting with remote execution.

