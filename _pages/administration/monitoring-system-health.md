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
You can monitor the health of your entire DCOS cluster from the system health page. The system health page provides the health status of all DCOS system components that are running in systemd.

You can access this system health utility from the DCOS web interface and drill down by health status, host IP address, or specific systemd unit.

You can click on each of the components to see a detailed view, including individual node health status and roles.

You can sort by system health. Possible health states are unhealthy and healthy. systemd units have have a binary health of 0 (healthy) or 1 (unhealthy). The unit is considered healthy if it is "active", "inactive", or not loaded, otherwise it is considered unhealthy.

**Healthy** All cluster nodes are healthy. The units are loaded and not in the "active" or "inactive" state.

**Unhealthy** One or more nodes have issues. The units are not loaded or are in the "active" or "inactive" state.

# System Health HTTP API Endpoint

The system health endpoint is exposed at port 1050:

    $ curl <host_ip>:1050/system/health/v1
    

## Troubleshooting

### SystemD unit state

The system health API assumes a systemd unit is healthy if it is loaded and not in the "active" or "inactive" state and is also loaded.

### Misinterpreting System Health by Unit

You're able to sort system health by systemd unit. However, this search can bring up misleading information as the service itself can be healthy but the node on which it runs is not. This manifests itself as a service showing "healthy" but nodes associated with that service as "unhealthy". Some people find this behavior confusing.

## Missing Cluster Hosts

The system health API relies on MesosDNS to know about all the cluster hosts. It finds these hosts by combining a query from `mesos.master` A records as well as `leader.mesos:5050/slaves` to get the complete list of hosts in the cluster.

This system has a known bug where a slave (agent) will not show up in the list returned from `leader.mesos:5050/slaves` if the Mesos slave service is not healthy. This means the system health API will not show this host.

If you experience this behavior it's most likely your Mesos slave service on the missing host is unhealthy.

# Troubleshooting

If you have any problems, you can check if the diagnostics service is running by SSH’ing to the Mesos leading master and checking the systemd status of the dcos-ddt.service: