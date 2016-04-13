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
This page contains terms and definitions for the Mesosphere DCOS.

### Admin router
The admin router runs on the DCOS master servers to provide a proxy for the admin parts of the cluster.

### <a name="agent"></a>Agent node### 
A Mesos agent node runs a discrete Mesos task on behalf of a framework. It is a agent instance registered with the Mesos master. The synonym of agent node is worker or slave node.

### Cloud template
The <a href="/tag/community/">DCOS Community Edition (CE) cloud templates</a> are optimized to run Mesosphere DCOS. The templates are a JSON-formatted text files that describe the resources and properties.

### Containerizer
The <a href="http://mesos.apache.org/documentation/latest/containerizer/">MesosContainerizer</a> provides lightweight containerization and resource isolation of executors using Linux-specific functionality such as cgroups and namespaces.

### Datacenter operating system
A new class of operating system that spans all of the machines in a datacenter or cloud and organizes them to act as one big computer.

### DCOS
The abbreviated form of the Mesosphere Datacenter Operating System.

### DCOS Cluster
A group of Mesos master and agent nodes.

### DCOS Marathon
The native Marathon instance that is the “init system” for DCOS. It starts and monitors DCOS applications and services. 

### DCOS service
DCOS services are Mesosphere-certified applications that are packaged and available from the public <a href="https://github.com/mesosphere/universe">GitHub package repositories</a>. Available DCOS services include Mesosphere-certified Mesos frameworks and other applications. A <a href="http://mesos.apache.org/documentation/latest/frameworks/">Mesos framework</a> is the combination of a Mesos scheduler and an optional custom executor.

### Executor
A framework running on top of Mesos consists of two components: a scheduler that registers with the master to be offered resources, and an executor process that is launched on slave nodes to run the framework’s tasks. For more information about framework schedulers and executors, see the <a href="http://mesos.apache.org/documentation/latest/app-framework-development-guide/">App/Framework development guide</a>.

### Exhibitor for Zookeeper
The DCOS uses ZooKeeper, a high-performance coordination service to manage the installed DCOS services. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DCOS installation.

### Framework
A Mesos framework is the combination of a Mesos scheduler and an optional custom executor. A framework receives resource offers describing CPU, RAM, etc., and allocates them for discrete tasks that can be launched on Mesos agent nodes. Mesosphere-certified Mesos frameworks, called DCOS services, are packaged and available from public <a href="https://github.com/mesosphere/universe">GitHub package repositories</a>. DCOS services include Mesosphere-certified Mesos frameworks and other applications.

### Master
A Mesos master aggregates resource offers from all <a href="#agent">agent nodes</a> and provides them to registered frameworks. For more details about the Mesos master, read about <a href="http://open.mesosphere.com/reference/mesos-master/" target="_blank">Mesos Master Configuration</a>.

### Mesos DNS
<a href="https://github.com/mesosphere/mesos-dns">Mesos DNS</a> is an open source DCOS component that provides service discovery within the DCOS cluster. Mesos-DNS allows applications and services that are running on Mesos to find each other by using the domain name system (DNS), similar to how services discover each other throughout the Internet.

### Package repository
DCOS services are Mesosphere-certified applications that are packaged and available from the public DCOS package repositories that are hosted on GitHub.

### Offer
An offer represents available resources (e.g. cpu, disk, memory) which an agent offers to the master and the master hands to the registered frameworks in some order.

### <a name="private"></a>Private agent node
The DCOS <a href="/dcosarchitecture/components/#scrollNav-2">private agent nodes</a> run the deployed apps and services.

### <a name="public"></a>Public agent node
The DCOS <a href="/dcosarchitecture/components/#scrollNav-2">public agent nodes</a> provide public access to DCOS applications that are run on a public agent node.

### Slave
The synonym of slave node is worker or agent node. A Mesos agent node runs a discrete Mesos task on behalf of a framework. It is a agent instance registered with the Mesos master. 

### State abstraction
Mesos provides an abstraction for accessing storage for schedulers for Java and C++ only.  This is the preferred method to access ZooKeeper.

### Task
A unit of work scheduled by a Mesos framework and executed on a Mesos agent. In Hadoop terminology, this is a "job". In MySQL terminology, this is a "query" or "statement". A task may simply be a Bash command or a Python script.

### Working directory
A Mesos master requires a directory on the local file system to write replica logs to.

### ZooKeeper<a name="zoo"></a>
The DCOS uses ZooKeeper, a high-performance coordination service to manage the installed DCOS services. Exhibitor automatically configures your ZooKeeper installation on the master nodes during your DCOS installation.

