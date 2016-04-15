---
UID: 56f9ad68e48cc
post_title: Monitoring System Health
post_excerpt: ""
layout: page
published: true
menu_order: 100
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: true
hide_from_related: true
---
You can monitor the health of your cluster components from the DCOS web interface component health page. 

The component health page provides the health status of all DCOS system components that are running in systemd. You can drill down by health status, host IP address, or specific systemd unit.

Possible health states are unhealthy and healthy. 

- **Healthy** All cluster nodes are healthy. The units are loaded and not in the "active" or "inactive" state.

- **Unhealthy** One or more nodes have issues. The units are not loaded or are in the "active" or "inactive" state.

![alt text](/assets/images/ui-sys-health.gif)

# System Health HTTP API Endpoint

The system health endpoint is exposed at port 1050:

    $ curl <host_ip>:1050/system/health/v1
    
# DCOS components

### Admin Router
The admin router is an open-source Nginx configuration created by Mesosphere that provides central authentication and proxy to DCOS services within the cluster.<!-- dcos-adminrouter.service/ -->

### Admin Router Reloader
Restarts the Admin router Nginx server so that it can pick up new DNS resolutions, for example `master.mesos` and `leader.mesos`.<!-- dcos-adminrouter-reload.service/ -->

### Admin Router Reloader Timer
Sets the Admin Router Reloader interval at once per hour.<!-- dcos-adminrouter-reload.timer/ -->

### Cluster ID
A randomly generated UUID that tracks individual cluster deployments.<!-- dcos-cluster-id.service/ -->

### Diagnostics
This component informs DCOS of individual node health for things like system resources as well as DCOS-specific services.<!-- dcos-ddt.service/ -->

### DNS Dispatcher
The DCOS DNS Dispatcher is an RFC5625 Compliant DNS Forwarder. It's job is to dual-dispatch DNS to multiple upstream resolvers, and to route DNS to the upstreams or Mesos DNS, depending on some rules.<!-- dcos-spartan.service/ -->
    
### DNS Dispatcher Watchdog
This service ensures that the DNS Dispatcher is running and healthy. If the DNS Dispatcher is unhealthy, this watchdog service kills it.<!-- dcos-spartan-watchdog.service/ -->
    
### DNS Dispatcher Watchdog Timer
This service wakes up the DNS Dispatcher Watchdog every 5 minutes, to see if DC/OS needs to restart DNS Dispatcher. <!-- dcos-spartan-watchdog.timer/ -->

### Erlang Port Mapping Daemon
This daemon acts as a name server on all hosts involved in distributed Erlang computations. For more information, see the [documentation](http://erlang.org/doc/man/epmd.html). <!-- dcos-epmd.service/ -->

### Exhibitor 
The Exhibitor supervisor for Zookeeper. <!-- dcos-exhibitor.service/ -->
  
### Generate resolv.conf
This is a service that helps the agent nodes locate the master nodes.<!-- dcos-gen-resolvconf.service/ -->

### Generate resolv.conf Timer
Periodically updates the systemd-resolved for Mesos DNS.<!-- dcos-gen-resolvconf.timer/ -->

### Identity and Access Management
Enterprise DCOS access control service. For more information, see the [documentation](/administration/security-and-authentication/).

### Keepalived
Runs keepalived to make a VRRP load balancer that can be used to access the masters.<!-- dcos-keepalived.service/ -->
  
### Layer 4 Load Balancer
The DC/OS Layer 4 Load Balancer that enables multi-tier microservices architectures. For more information, see the [documentation](usage/service-discovery/load-balancing/).<!-- dcos-minuteman.service/ -->


### Logrotate
Logrotate allows for the automatic rotation compression, removal, and mailing of log files.<!-- dcos-logrotate.service/ -->

### Logrotate Timer
Sets the logrotate interval at 2 minutes. <!-- dcos-logrotate.timer/ -->

### Marathon
The DCOS Marathon instance starts and monitors DCOS applications and services.<!-- dcos-marathon.service/ -->

### Mesos Agent
The mesos-slave process for [private](/overview/concepts/#private) agent nodes.<!-- dcos-mesos-slave.service/ -->

### Mesos Agent Public
The mesos-slave process for [public](/overview/concepts/#public) agent nodes.<!-- dcos-mesos-slave-public.service/ -->

### Mesos DNS
Mesos DNS provides service discovery within the cluster.<!-- dcos-mesos-dns.service/ -->

### Mesos History
Enables the DCOS web interface to display cluster usage statistics.<!-- dcos-history-service.service/ -->
  
### Mesos Master
The mesos-master process orchestrates agent tasks.<!-- dcos-mesos-master.service/ -->

### Mesos Persistent Volume Discovery
During DC/OS startup, this service connects to existing Mesos volume mounts on agent nodes. For more information on Mesos Persistent Volumes, see the [documentation](http://mesos.apache.org/documentation/latest/persistent-volume/). <!-- dcos-vol-discovery-pub-agent.service/ -->

### Network Metrics Aggregator
Collects statistics from the Layer 4 Load Balancer and displays them in the DC/OS Network tab.<!-- dcos-networking_api.service/ -->

### OAuth
DC/OS authorization service. <!-- dcos-oauth.service/ -->

### Package Service
This process manage the Universe package repositories. <!-- dcos-cosmos.service/ -->

### Signal
Sends a periodic ping back to Mesosphere with high-level cluster information to help improve DCOS, and provides advanced monitoring of cluster issues.<!-- dcos-signal.service/ -->

### Signal Timer
Sets the Signal component interval at once per hour.<!-- dcos-signal.timer/ -->