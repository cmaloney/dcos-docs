---
post_title: Advanced Installer
menu_order: 2
---
With this installation method, you package the DCOS distribution yourself and connect to every node manually to run the DCOS installation commands. This installation method is recommended if you want to integrate with an existing system or if you don’t have SSH access to your cluster.

The advanced installer requires:

  * The bootstrap node must be network accessible from the cluster nodes.
  * The bootstrap node must have the HTTP(S) ports open from the cluster nodes.