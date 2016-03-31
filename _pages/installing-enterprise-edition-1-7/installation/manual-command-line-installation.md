---
UID: 56f98445082e6
post_title: Manual installer
post_excerpt: ""
layout: page
published: true
menu_order: 3
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
With the manual installer, you package the DCOS distribution yourself and connect to every node manually to run the DCOS installation commands. This installation method is recommended if you want to integrate with an existing system or if you don’t have SSH access to your cluster.

The manual installer requires:

- The bootstrap node must be network accessible from the cluster nodes.
- The bootstrap node must have the HTTP(S) ports open from the cluster nodes.
