---
post_title: Components
menu_order: 1
---
DC/OS is built on top of the following open-source components.

## Mesos

<a href="https://mesosphere.com/why-mesos/" target="_blank">Mesos</a> is the distributed systems kernel that is at the core of DC/OS. Mesos provides efficient resource isolation and sharing across distributed applications or frameworks. Mesos pools your infrastructure, automatically allocating resources and scheduling tasks based on demands and policy.

Mesosphere is a significant contributor to the Apache Mesos project and is the primary organization writing open-source services for Mesos, such as Marathon and Chronos.

## Marathon

<a href="http://mesosphere.github.io/marathon/" target="_blank">Marathon</a> is the DC/OS container-orchestration engine that provides cluster-wide init and control for microservices in cgroups or Docker containers. Marathon is an open-source Mesos framework created by Mesosphere that launches long-running applications for DC/OS. It starts and manages applications and services inside of Mesos, automatically healing failures.

## Mesos-DNS

<a href="https://github.com/mesosphere/mesos-dns" target="_blank">Mesos-DNS</a> provides service discovery in DC/OS so that applications and services can find each other. Mesos-DNS allows applications and services that are running on Mesos to find each other by using the domain name system (DNS), similar to how services discover each other throughout the Internet.

## ZooKeeper

ZooKeeper is a high-performance coordination service that manages the DC/OS services. To coordinate and manage the ZooKeeper system, we've integrated <a href="https://github.com/Netflix/exhibitor" target="_blank">Exhibitor for ZooKeeper</a>.

## Admin Router

The <a href="https://github.com/mesosphere/adminrouter-public" target="_blank">admin router</a> is an open-source Nginx configuration created by Mesosphere that provides central authentication and proxy to DC/OS services within the cluster.