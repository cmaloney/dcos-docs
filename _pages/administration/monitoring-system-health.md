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



# Troubleshooting

If you have any problems, you can check if the diagnostics service is running by SSH’ing to the Mesos leading master and checking the systemd status of the `dcos-ddt.service`.