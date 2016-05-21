---
UID: 56f98445387d2
post_title: Using Virtual IP Addresses
post_excerpt: ""
layout: page
published: true
menu_order: 100
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
DC/OS can map traffic from a single Virtual IP (VIP) to multiple IP addresses and ports.

You can assign a VIP to your application by using the DC/OS Marathon web interface. The values you enter in these fields are translated into the appropriate `portMapping` entry in your application definition. Toggle to `JSON mode` as you create your app to see and edit your application definition.

## Prerequisite:

*   A pool of VIP addresses that are unique to your application.


## Creating a VIP

1.  From the DC/OS web interface, click on the **Services** tab and select **marathon**.
    
    *   To create a new application, click **Create Application** and select the **Ports and Service Discovery** menu option. 
    *   To edit an existing application, select your application and click the **Configuration** tab, then click **Edit**. You can then select the **Ports and Service Discovery** menu option. 
    
![Ports in Marathon UI](/assets/images/ui-marathon-ports.png)

1.  Enter the Port, Protocol, Name, and VIP address. Enter the VIP in `<IP>:<PORT>` format.
    
    **Tip:** Select **JSON Mode** to edit your application directly in JSON.
    
    For more information on port configuration, see the [ports documentation][1].

1.  From inside the cluster, you will be able to use your VIP directly. You can SSH into the cluster and run this command to see it work:
    
        curl 1.1.1.1:5000
        

## Assigning Multiple VIPs to Your App

To assign multiple VIPs to your application, switch to JSON mode and add the additional VIPs to your `portDefinitions`. In this example, the additional VIP added is `"VIP_1": "111.2.1.23:8080"`:

    {
      "id": null,
      "cmd": "",
      "cpus": 1,
      "mem": 128,
      "disk": 0,
      "instances": 1,
      "ports": [
        0
      ],
      "portDefinitions": [
        {
          "port": 3333,
          "protocol": "tcp",
          "name": "test",
          "labels": {
            "VIP_0": "111.2.1.23:80",
            "VIP_1": "111.2.1.23:8080"
          }
        },
        {
          "port": 0,
          "protocol": "tcp",
          "name": null,
          "labels": null
        }
      ]
    }
    
## Monitoring network health

You can monitor the health of your VIPs from the **Network** tab on the DC/OS web interface. 

You can click on VIPs to see a detailed view. 

- The **Successes and Failures per Minute** graph shows the number of successful and failed tasks over the past minute. 
  ![VIP details Successes and Failures per Minute](/assets/images/network-2.png)  
- The Connection Latency per Minute graph shows the connection latency over the past hour.
  ![VIP details connection latency over the past hour](/assets/images/network-3.png)
- You can click on individual backends to see VIP client details as well.
  ![VIP details backend](/assets/images/network-4.png)


 [1]: http://mesosphere.github.io/marathon/docs/ports.html
