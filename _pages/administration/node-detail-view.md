---
post_title: Viewing the node details
post_excerpt: ""
layout: page
published: true
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---

You can easily get a detailed view of the services that are running on each node in your DCOS cluster. 

[tutorial-prereq]



1.  SSH into your master node. For example, if your master is ``:

        $ dcos node ssh --master 

2.  Issue this command:

        $ curl http://master.mesos:8123/v1/enumerate