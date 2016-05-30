---
layout: page
post_title: Install DC/OS with Vagrant
menu_order: 0
---

This installation method uses Vagrant to create a cluster of virtual machines on your local machine that can be used for demos, development, and testing with DC/OS.

# 1. Download DC/OS Installer

First, it's necessary to download the [DC/OS 1.7.0 Installer][1]. Save this somewhere safe - you'll need this when setting up DC/OS Vagrant:

    $ curl -O https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh
    

See the [DC/OS Installer Release List][2] for other versions.

# 2. Install DC/OS Vagrant

DC/OS Vagrant installation and usage instructions are maintained in the dcos-vagrant GitHub repository.

Follow the the [DC/OS Vagrant 0.6.0][3] instructions to set up your host machine correctly and to install DC/OS.

Note that you will require at least 5GB of free memory to run DC/OS via this method.

Or see the [DC/OS Vagrant Release List][4] for other versions.

 [1]: https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh
 [2]: https://dcos.io/releases/
 [3]: https://github.com/dcos/dcos-vagrant/tree/v0.6.0
 [4]: https://github.com/mesosphere/dcos-vagrant/releases