---
title: Ordering & Requisites
permalink: config/requisites.html
type: page
layout: getstarted.tmpl
series: SaltStack Configuration Management
step: 5
overview:
  goals:
    - Understand the default execution order
    - Use requisites to declare execution order 
  time: 15
  difficulty: 2
---

{: section sidebar :}

#### Declarative and Imperative

SaltStack supports both of the leading methodologies to define system
configurations.

Follow a declarative methodology using SaltStack's powerful requisites
system, or go imperative with SaltStacks built-in ordered execution.

{: end sidebar :}

## Ordering

One of the most important (and complex) aspects of configuration
management is making sure that each task is executed at the correct
time.

By default, each ID in a Salt state file is executed in the order it appears
in the file. Additionally, in the Top file, each Salt state file is applied in
the order listed. For example, the state IDs in the following diagram run in
sequential order as a result of the organization of the Top file and Salt state files:

![]({{ conf.images }}/imperative.png)

By organizing the Salt states that you want applied in this order, you can
control execution order without explicitly defining dependencies.

## Requisites

Requisites let you explicitly specify the dependencies for an ID in a
state. If you add a statement that indicates `id1` requires `id4`, `id4`
is applied first.

![]({{ conf.images }}/declarative.png)

You can use the `state.show_sls` execution function to view the order in which
Salt states are applied. For example:

~~~ bash
salt 'minion1' state.show_sls sls1[,sls2,...] 
~~~
To view the order in which Salt states are applied from the `examples.sls` file you created earlier:

~~~ bash
salt 'minion1' state.show_sls examples
~~~

## Beyond Require

There are several other statements you can use to control execution order. You
can find details in [Requisites and other Global State
Arguments](https://docs.saltstack.com/en/latest/ref/states/requisites.html).

