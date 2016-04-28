---
UID: 56f98449d141e
post_title: Concepts
post_excerpt: ""
layout: page
published: true
menu_order: 3
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
This page contains terms and definitions for the Mesosphere DC/OS.

### Admin router

The admin router runs on the DC/OS master servers to provide a proxy for the admin parts of the cluster.

### <a name="agent"></a>Agent node

A Mesos agent node runs a discrete Mesos task on behalf of a framework. It is a agent instance registered with the Mesos master. The synonym of agent node is worker or slave node. See also [private][1] and [public][2] agent nodes.

### Cloud template

The [DC/OS Community Edition (CE) cloud templates][3] are optimized to run Mesosphere DC/OS. The templates are a JSON-formatted text files that describe the resources and properties.

### Containerizer

The [MesosContainerizer][4] provides lightweight containerization and resource isolation of executors using Linux-specific functionality such as cgroups and namespaces.

### Datacenter operating system

A new class of operating system that spans all of the machines in a datacenter or cloud and organizes them to act as one big computer.

### DC/OS

The abbreviated form of the Mesosphere Datacenter Operating System.

### DC/OS Cluster

A group of Mesos master and agent nodes.

### DC/OS Marathon

The native Marathon instance that is the “init system” for DC/OS. It starts and monitors DC/OS applications and services.

### DC/OS service

DC/OS services are Mesosphere-certified applications that are packaged and available from the public [GitHub package repositories][5]. Available DC/OS services include Mesosphere-certified Mesos frameworks and other applications. A [Mesos framework][6] is the combination of a Mesos scheduler and an optional custom executor.

### Executor

A framework running on top of Mesos consists of two components: a scheduler that registers with the master to be offered resources, and an executor process that is launched on slave nodes to run the framework’s tasks. For more information about framework schedulers and executors, see the [App/Framework development guide][7].

### Exhibitor for Zookeeper

The DC/OS uses ZooKeeper, a high-performance coordination service to manage the installed DC/OS services. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DC/OS installation.

### Framework

A Mesos framework is the combination of a Mesos scheduler and an optional custom executor. A framework receives resource offers describing CPU, RAM, etc., and allocates them for discrete tasks that can be launched on Mesos agent nodes. Mesosphere-certified Mesos frameworks, called DC/OS services, are packaged and available from public [GitHub package repositories][5]. DC/OS services include Mesosphere-certified Mesos frameworks and other applications.

### Master

A Mesos master aggregates resource offers from all [agent nodes][8] and provides them to registered frameworks. For more details about the Mesos master, read about <a href="http://open.mesosphere.com/reference/mesos-master/" target="_blank">Mesos Master Configuration</a>.

### Mesos DNS

[Mesos DNS][9] is an open source DC/OS component that provides service discovery within the DC/OS cluster. Mesos-DNS allows applications and services that are running on Mesos to find each other by using the domain name system (DNS), similar to how services discover each other throughout the Internet.

### Package repository

DC/OS services are Mesosphere-certified applications that are packaged and available from the public DC/OS package repositories that are hosted on GitHub.

### Offer

An offer represents available resources (e.g. cpu, disk, memory) which an agent offers to the master and the master hands to the registered frameworks in some order.

### <a name="private"></a>Private agent node

Private agent nodes run DC/OS apps and services through a non-routable network that is only accessible from the admin zone or through the edgerouter from the public zone. By default DC/OS launches apps on private agent nodes. DC/OS agent nodes can be designated as [public][10] or [private][11] during installation. For more information, see the Network Security [documentation][12].

### <a name="public"></a>Public agent node

Public agent nodes run DC/OS apps and services in a publicly accessible network. DC/OS agent nodes can be designated as [public][10] or [private][11] during installation. For more information, see the Network Security [documentation][12].

### Slave

The synonym of slave node is worker or agent node. A Mesos agent node runs a discrete Mesos task on behalf of a framework. It is a agent instance registered with the Mesos master.

### State abstraction

Mesos provides an abstraction for accessing storage for schedulers for Java and C++ only. This is the preferred method to access ZooKeeper.

### Task

A unit of work scheduled by a Mesos framework and executed on a Mesos agent. In Hadoop terminology, this is a "job". In MySQL terminology, this is a "query" or "statement". A task may simply be a Bash command or a Python script.

### Working directory

A Mesos master requires a directory on the local file system to write replica logs to.

### ZooKeeper<a name="zoo"></a>

The DC/OS uses ZooKeeper, a high-performance coordination service to manage the installed DC/OS services. Exhibitor automatically configures your ZooKeeper installation on the master nodes during your DC/OS installation.

 [1]: #private
 [2]: #public
 [3]: /tag/community/
 [4]: http://mesos.apache.org/documentation/latest/containerizer/
 [5]: https://github.com/mesosphere/universe
 [6]: http://mesos.apache.org/documentation/latest/frameworks/
 [7]: http://mesos.apache.org/documentation/latest/app-framework-development-guide/
 [8]: #agent
 [9]: https://github.com/mesosphere/mesos-dns
 [10]: /overview/concepts/#public
 [11]: /overview/concepts/#private
 [12]: /overview/security/