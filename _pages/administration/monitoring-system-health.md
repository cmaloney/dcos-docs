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
<!-- dcos-adminrouter.service/ -->
The admin router is an open-source Nginx configuration created by Mesosphere that provides central authentication and proxy to DCOS services within the cluster.

### Admin Router Reloader
<!-- dcos-adminrouter-reload.service/ -->
Restarts the Admin router Nginx server so that it can pick up new DNS resolutions, for example `master.mesos` and `leader.mesos`.

### Admin Router Reloader Timer
<!-- dcos-adminrouter-reload.timer/ -->
Sets the Admin Router Reloader interval at once per hour.

### Cluster ID
<!-- dcos-cluster-id.service/ -->

### Diagnostics
<!-- dcos-ddt.service/ -->
This component informs DCOS of individual node health for things like system resources as well as DCOS-specific services.

### DNS Dispatcher
<!-- dcos-spartan.service/ -->
The DCOS DNS Dispatcher is an RFC5625 Compliant DNS Forwarder. It's job is to dual-dispatch DNS to multiple upstream resolvers, and to route DNS to the upstreams or Mesos DNS, depending on some rules.
    
### DNS Dispatcher Watchdog
<!-- dcos-spartan-watchdog.service/ -->
This service ensures that the DNS Dispatcher is running and healthy. If the DNS Dispatcher is unhealthy, this watchdog service kills it.
    
### DNS Dispatcher Watchdog Timer
<!-- dcos-spartan-watchdog.timer/ -->
This service wakes up the DNS Dispatcher Watchdog every 5 minutes, to see if DC/OS needs to restart DNS Dispatcher.

### Erlang Port Mapping Daemon
<!-- dcos-epmd.service/ -->
This daemon acts as a name server on all hosts involved in distributed Erlang computations. For more information, see the [documentation](http://erlang.org/doc/man/epmd.html).

### Exhibitor
<!-- dcos-exhibitor.service/ -->
The Exhibitor supervisor for Zookeeper.
  
### Generate resolv.conf
<!-- dcos-gen-resolvconf.service/ -->
This is a service that helps the agent nodes locate the master nodes.

### Generate resolv.conf Timer
<!-- dcos-gen-resolvconf.timer/ -->
Periodically updates the systemd-resolved for Mesos DNS.

### Identity and Access Management
Enterprise DCOS access control service. For more information, see the [documentation](/administration/security-and-authentication/).

### Keepalived
<!-- dcos-keepalived.service/ -->
Runs keepalived to make a VRRP load balancer that can be used to access the masters.
  
### Layer 4 Load Balancer
<!-- dcos-minuteman.service/ -->

### Logrotate
<!-- dcos-logrotate.service/ -->
Logrotate allows for the automatic rotation compression, removal, and mailing of log files.

### Logrotate Timer
<!-- dcos-logrotate.timer/ -->
Sets the logrotate interval at 2 minutes.

### Marathon
<!-- dcos-marathon.service/ -->
The DCOS Marathon instance starts and monitors DCOS applications and services.

### Mesos Agent
<!-- dcos-mesos-slave.service/ -->
The mesos-slave process.

### Mesos Agent Public
<!-- dcos-mesos-slave-public.service/ -->

### Mesos DNS
<!-- dcos-mesos-dns.service/ -->
Mesos DNS provides service discovery within the cluster.

### Mesos History
<!-- dcos-history-service.service/ -->
Enables the DCOS web interface to display cluster usage statistics.
  
### Mesos Master
<!-- dcos-mesos-master.service/ -->
The mesos-master process orchestrates agent tasks.

### Mesos Persistent Volume Discovery
<!-- dcos-vol-discovery-pub-agent.service/ -->

### OAuth
<!-- dcos-oauth.service/ -->
DC/OS authorization service.

### Package Service
<!-- dcos-cosmos.service/ -->
This process manage the Universe package repositories. 

### Signal
<!-- dcos-signal.service/ -->
Sends a periodic ping back to Mesosphere with high-level cluster information to help improve DCOS, and provides advanced monitoring of cluster issues.

### Signal Timer
<!-- dcos-signal.timer/ -->
Sets the Signal component interval at once per hour.