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

DCOS agent nodes are designated as [public](/overview/concepts/#public) or [private](/overview/concepts/#private). Public agent nodes can provide public access to DCOS applications. By default apps are launched on private agent nodes. The Admin Router provides a proxy from your apps on private nodes to public nodes. 

You can designate your agent nodes as public or private during [advanced installation](/administration/installing/custom/) or you can declare as a Marathon [app definition]() by using the `"acceptedResourceRoles":["slave_public"]` parameter. For more information about the `acceptedResourceRoles` parameter, see the Marathon REST API [documentation](https://mesosphere.github.io/marathon/docs/rest-api.html).

The native Marathon instance is configured to receive offers from the public agent nodes (`--mesos_role="slave_public"`). Marathon apps are configured to use unreserved resources and launch on private agent nodes (`--default_accepted_resource_roles="*"`).

*   To run a Marathon app on public agent nodes, you must set the `acceptedResourceRoles` property to:
    
        "acceptedResourceRoles":["slave_public"]

*   To run a Marathon app on any agent node type, you must set the `acceptedResourceRoles` property to:
    
        "acceptedResourceRoles":["slave_public", "*"]

For a comprehensive example of deploying an app in the public zone to route HTTP requests, see [Deploying a Containerized App on a Public Node][1].

 [1]: /tutorials/containerized-app/