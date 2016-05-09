---
post_title: 'Private: Configuring Public Access to an App'
menu_order: 51
---
This tutorial configures public access to the `demo` app created in the [Adding a custom Docker App to Marathon][1] tutorial. The `demo` app is made accessible in the DCOS public security zone and the public internet.

To configure public access, a Docker image is created that contains your customized <a href="https://raw.githubusercontent.com/mesosphere/oinker/shakespeare/router/marathon.json" target="_blank">Oinker router</a> specified and then load your router to Marathon by using DCOS.

The Oinker router sets the public worker node security role to `slave_public` to reserve its resources with `acceptedResourceRoles": ["slave_public"]`. For a description of the DCOS security zones, see [DCOS Security][2].

**Prerequisites**

*   The [demo-app][1] tutorial
*   A Docker account

# Customize router and upload to Docker

1.  Clone the Oinker router repository and switch to the shakespeare branch:
    
        $ git clone https://github.com/mesosphere/oinker.git && cd oinker
        $ git checkout shakespeare
        

2.  Navigate to the `/oinker/router/app.lua` file and replace `oinker` with your app name and save. In this example `oinker` is replaced with `demo`:
    
        local cjson = require "cjson"
        
        resp = ngx.location.capture('/__mesos_dns/v1/services/_demo._tcp.marathon.mesos')
        
        backends = cjson.decode(resp.body)
        backend = backends[1]
        ngx.var.target = "http://" .. backend['ip'] .. ":"  .. backend['port']
        

3.  From the `/oinker/router/` directory, build a new Docker image:
    
    **Tip:** To run this command you must be logged into to Docker with Boot2Docker.
    
        $ docker build -t <mydockerhubusername>/demo-router .
        

4.  Push your Docker image to Docker Hub:
    
    **Tip:** To run this command you must be logged into to Docker with Boot2Docker.
    
        $ docker push <mydockerhubusername>/demo-router
        

# Add router to Marathon

1.  Add the `demo-router` Docker filename to the `/oinker/router/marathon.json` file and save.
    
    **Important:** Do not modify the other properties.
    
        {
          "id": "/demo-router",
          "cpus": 1,
          "mem": 256,
          "instances": 1,
          "constraints": [["hostname", "UNIQUE"]],
          "acceptedResourceRoles": ["slave_public"],
          "container": {
            "type": "DOCKER",
            "docker": {
              "image": "<mydockerhubusername>/demo-router",
              "network": "BRIDGE",
              "portMappings": [
                  {
                      "containerPort": 8080,
                      "hostPort": 80,
                      "protocol": "tcp"
                  }
              ]
            }
          },
          "healthChecks": [{
              "protocol": "TCP",
              "gracePeriodSeconds": 600,
              "intervalSeconds": 30,
              "portIndex": 0,
              "timeoutSeconds": 10,
              "maxConsecutiveFailures": 2
          }]
        }
        

2.  Copy your `/oinker/router/marathon.json` file to your DCOS installation directory.

3.  From the DCOS CLI, add the customized Oinker router to Marathon:
    
        $ dcos marathon app add marathon.json
        

4.  Verify that the **demo-router** app is added to Marathon.
    
    *   From the DCOS CLI:
        
            $ dcos marathon app list ID MEM CPUS DEPLOYMENTS TASKS CONTAINER CMD  
            /demo-router 256.0 1.0 1 0/1 DOCKER []
            
    
    *   From the DCOS web interface **Services** tab, click on **Marathon** Service to confirm that your app is added to Marathon:
        
        <a href="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/newapptutorial.png" rel="attachment wp-att-1564"><img src="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/newapptutorial.png" alt="newapptutorial" width="664" height="127" class="alignnone size-full wp-image-1564" /></a>
    
    **Tip:** For troubleshooting information, see the <a href="https://support.mesosphere.com/hc/en-us/articles/205425545-How-do-I-troubleshoot-DCOS-issues-" target="_blank">Mesosphere Knowledge Base</a>.

# Navigate to the app

The demo app is is routed to the public Mesos agent node.

1.  From the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page, click to check the box next to your stack.

2.  Click on the **Outputs** tab and copy the PublicSlaveDnsAddress value into the buffer.
    
    <a href="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/awsec2privatedns.png" rel="attachment wp-att-1496"><img src="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/awsec2privatedns-600x148.png" alt="awsec2privatedns" width="500" height="274" class="alignnone size-medium wp-image-1496" /></a>

3.  Paste the address into your browser to open the web interface for your app.
    
    <a href="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/screenshot-demo-app.png" rel="attachment wp-att-1582"><img src="http://live-mesosphere-documentation.pantheon.io/wp-content/uploads/2015/12/screenshot-demo-app.png" alt="screenshot-demo-app" width="641" height="446" class="alignnone size-full wp-image-1582" /></a>

 [1]: ../deploying-a-custom-docker-app-on-marathon/
 [2]: /security/