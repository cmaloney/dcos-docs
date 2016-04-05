---
UID: 5703eac5a3c37
post_title: Automated Installer
post_excerpt: ""
layout: page
published: true
menu_order: 1
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
The automated installer provides a simple graphical interface that guides you through the installation of DCOS. The automated installer provides a basic installation that is suitable for demonstrations and POCs. This is the fastest way to get started with DCOS.

The automated installer uses a bootstrap node to administer the DCOS installation across your cluster. The bootstrap node uses an SSH key to connect to each node in your cluster to automate the DCOS installation.

The automated installer requires:

*   Cluster nodes must be network accessible from the bootstrap node.
*   Cluster nodes must have SSH enabled and ports open from the bootstrap node.

The bootstrap node must have an unencrypted SSH key that can be used to authenticate with the cluster nodes over SSH.