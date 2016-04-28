---
UID: 56f98447932f0
post_title: Architecture
post_excerpt: ""
layout: page
published: true
menu_order: 2
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
The Mesosphere DC/OS is comprised of Mesos master and agent nodes, a native DC/OS Marathon instance, Mesos-DNS for service discovery, Admin Router for central authentication and proxy to DC/OS services, and Zookeeper to coordinate and manage the installed DC/OS services.

<img src="/wp-content/uploads/2015/12/Enterprise-Architecture-Diagram.png" alt="Enterprise Architecture Diagram" width="2468" height="1420" class="alignnone size-full wp-image-834" />

# DC/OS master nodes

The DC/OS master node includes the Mesos master process, Mesos-DNS, DC/OS Marathon, ZooKeeper, and the Admin Router.

When a leading master fails due to a crash or goes offline for an upgrade, a standby master automatically becomes the leader without causing any disruption to running services. Leader election is performed by using ZooKeeper.

### Mesos master process

The `mesos-master` process orchestrates tasks that are run on agent nodes. The Mesos master process receives resource offers from agents and distributes those resources to registered DC/OS services, such as Marathon or Chronos. For more information, see the [Mesos Master Configuration][1] documentation.

### Mesos DNS

Mesos DNS provides service discovery within the cluster. Mesos DNS allows applications and services that are running on Mesos to find each other by using the domain name system (DNS), similar to how services discover each other throughout the Internet.

### DC/OS Marathon

The native Marathon instance that is the “init system” for DC/OS. It starts and monitors DC/OS applications and services.

### ZooKeeper

ZooKeeper is a high-performance coordination service that manages the DC/OS services. Exhibitor automatically configures your Zookeeper installation on the master nodes during installation.

### Admin router

The [admin router][2] is an open source Nginx configuration created by Mesosphere that provides central authentication and proxy to DC/OS services within the cluster.

# DC/OS agent nodes

The DC/OS private agent nodes run the deployed apps and services. The optional DC/OS public agent nodes can provide public access to DC/OS applications that are run on a public agent node.

### Mesos agent process

The `mesos-slave` process offers up available resources on a node to the Mesos masters. The agent node also accepts schedule requests from the master and invokes an executor to launch a task. For more information, see the [Mesos Slave Configuration][3] documentation.

### Mesos containerizer

The Mesos Containerizer provides lightweight containerization and resource isolation of executors using Linux-specific functionality such as cgroups and namespaces. For more information, see the [Mesos Containerizer][4] documentation.

### Docker container

The Mesos Docker Containerizer provides support for launching tasks that contain Docker images. For more information, see the [Docker Containerizer][5] documentation.

 [1]: https://open.mesosphere.com/reference/mesos-master/
 [2]: https://github.com/mesosphere/adminrouter-public
 [3]: https://open.mesosphere.com/reference/mesos-slave/
 [4]: http://mesos.apache.org/documentation/latest/containerizer/
 [5]: http://mesos.apache.org/documentation/latest/docker-containerizer/