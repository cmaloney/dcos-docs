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

DCOS agent nodes can be designated as [public](/overview/concepts/#public) or [private](/overview/concepts/#private). Public agent nodes can provide public access to DCOS applications. By default apps are launched on private agent nodes and the Admin Router provides a proxy from your apps on private nodes to public nodes. 

You can designate agent nodes as public or private during [advanced installation](/administration/installing/custom/) or after you are up and running by creating a Marathon app definition with the `"acceptedResourceRoles":["slave_public"]` parameter included. For example:

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

For more information about the `acceptedResourceRoles` parameter, see the Marathon REST API [documentation](https://mesosphere.github.io/marathon/docs/rest-api.html). For a comprehensive example of deploying an app in the public zone to route HTTP requests, see [Deploying a Containerized App on a Public Node][1].

 [1]: /tutorials/containerized-app/