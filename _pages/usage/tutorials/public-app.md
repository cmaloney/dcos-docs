---
UID: 56f98449df041
post_title: Running Your App on Public Nodes
post_excerpt: ""
layout: page
published: true
menu_order: 12
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
DC/OS agent nodes can be designated as [public][1] or [private][2] during [advanced][3] or [cloud][4] installations. Public agent nodes provide public access to your DC/OS applications. By default apps are launched on private agent nodes. To launch an app on a public node, you must create a Marathon app definition with the `"acceptedResourceRoles":["slave_public"]` parameter specified.

1.  Install DC/OS and DC/OS CLI by using the [advanced installation][3] or [cloud installation][4] instructions. You must declare at least one agent node as public.
    
    For example, with advanced installation you can designate an agent node with this command:
    
        $ sudo bash dcos_install.sh slave_public
        
    
    For example, with the AWS cloud installation, you can specify a public agent node with the `PublicSlaveInstanceCount` box:
    
    ![alt text][5]

2.  Create a Marathon app definition with the `"acceptedResourceRoles":["slave_public"]` parameter specified. For example:
    
        {
            "id": "/product/service/myApp",
            "container": {
            "type": "DOCKER",
            "docker": {
                  "image": "group/image",
                  "network": "BRIDGE",
                  "portMappings": [
                    { "hostPort": 80, "containerPort": 80, "protocol": "tcp"}
                  ]
                }
            },
            "acceptedResourceRoles": ["slave_public"],
            "instances": 1,
            "cpus": 0.1,
            "mem": 64
        }
        
    
    For more information about the `acceptedResourceRoles` parameter, see the Marathon REST API [documentation][6].

3.  Add the your app to Marathon by using this command:
    
        $ dcos marathon app add myApp.json
        
    
    If this is added successfully, there is no output.

4.  Verify that the app is added:
    
        $ dcos marathon app list
        ID      MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  CONTAINER  CMD                        
        /nginx   64  0.1    0/1    ---      scale       DOCKER   None

 [1]: /overview/concepts/#public
 [2]: /overview/concepts/#private
 [3]: /administration/installing/custom/
 [4]: /administration/installing/cloud/
 [5]: /assets/images/dcos-aws-step2c.png
 [6]: https://mesosphere.github.io/marathon/docs/rest-api.html