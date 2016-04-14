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
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-adminrouter-reload.service/ -->
Restarts the Admin router Nginx server so that it can pick up new DNS resolutions, for example `master.mesos` and `leader.mesos`.

### Admin Router Reloader Timer
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-adminrouter-reload.timer/ -->
Sets the Admin Router Reloader interval at once per hour.

### Cluster ID
<!-- dcos-cluster-id.service/ -->

### Diagnostics
<!-- dcos-ddt.service/ -->
This component informs DCOS of individual node health for things like system resources as well as DCOS-specific services.

### DNS Dispatcher
<!-- dcos-spartan.service/ -->
    
### DNS Dispatcher Watchdog
<!-- dcos-spartan-watchdog.service/ -->
    
### DNS Dispatcher Watchdog Timer
<!-- dcos-spartan-watchdog.timer/ -->

### DNS Spartan
<!-- dcos-spartan.service/ -->

### Erlang Port Mapping Daemon
<!-- dcos-epmd.service/ -->
This daemon acts as a name server on all hosts involved in distributed Erlang computations. For more information, see the [documentation](http://erlang.org/doc/man/epmd.html).

### Exhibitor
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-exhibitor.service/ -->
The Exhibitor supervisor for Zookeeper.
  
### Generate resolv.conf
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-gen-resolvconf.service/ -->
This is a service that helps the agent nodes locate the master nodes.

### Generate resolv.conf Timer
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-gen-resolvconf.timer/ -->
Periodically updates the systemd-resolved for Mesos DNS.

### Identity and Access Management
Enterprise DCOS access control service. For more information, see the [documentation](/administration/security-and-authentication/).

### Keepalived
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-keepalived.service/ -->
Runs keepalived to make a VRRP load balancer that can be used to access the masters.
  
### Layer 4 Load Balancer
<!-- dcos-minuteman.service/ -->

### Logrotate
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-logrotate.service/ -->
Logrotate allows for the automatic rotation compression, removal, and mailing of log files.

### Logrotate Timer
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-logrotate.timer/ -->
Sets the logrotate interval at 2 minutes.

### Marathon
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-marathon.service/ -->
The DCOS Marathon instance starts and monitors DCOS applications and services.

### Mesos Agent
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-mesos-slave.service/ -->
The mesos-slave process.

### Mesos Agent Public
<!-- dcos-mesos-slave-public.service/ -->

### Mesos DNS
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-mesos-dns.service/ -->
Mesos DNS provides service discovery within the cluster.

### Mesos History
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-history-service.service/ -->
Enables the DCOS web interface to display cluster usage statistics.
  
### Mesos Master
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-mesos-master.service/ -->
The mesos-master process orchestrates agent tasks.

### Mesos Persistent Volume Discovery
<!-- dcos-vol-discovery-pub-agent.service/ -->

### OAuth
<!-- dcos-oauth.service/ -->
DC/OS authorization service.

### Package Service
<!-- dcos-cosmos.service/ -->

### Signal
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-signal.service/ -->
Sends a periodic ping back to Mesosphere with high-level cluster information to help improve DCOS, and provides advanced monitoring of cluster issues.

### Signal Timer
<!-- http://joel-hmtx-elasticl-go3zjvu2iju-1084638227.us-west-2.elb.amazonaws.com/#/system/overview/components/dcos-signal.timer/ -->
Sets the Signal component interval at once per hour.

  
### dcos-link-env.service
Makes vendored DCOS binaries, such as the mesos-master, mesos-slave, available at the command line when SSHing to a host.
  
### dcos-download.service
Downloads and extracts the DCOS bootstrap tarball into `/opt/mesosphere` onto your bootstrap node during installation.

### dcos-setup.service
Specializes the DCOS bootstrap tarball for the particular cluster, as well as its cluster role.
 
### dcos-nginx.service
A high performance web server and a reverse proxy server.


# Troubleshooting

If you have any problems, you can check if the diagnostics service is running by SSH’ing to the Mesos leading master and checking the systemd status of the `dcos-ddt.service`.