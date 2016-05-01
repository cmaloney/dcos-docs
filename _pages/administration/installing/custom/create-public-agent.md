---
post_title: Converting a Private Node to Public  
layout: page
published: true
---

In DC/OS, we distinguish between agents that are accessible from the cloud and those which are not with the notion of "public agents". By default, agent nodes are designated as private during [GUI](/administration/installing/custom/gui/) or [CLI](/administration/installing/custom/cli/) installation.

You can determine the count of public agents in your cluster by running the following command. A result of 0 means that you do not have a public agent, as shown below:

    $ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)/mesos/master/slaves | jq '.slaves[] | .reserved_resources' | grep slave_public | wc -l
           0


### Prerequisites:

1. DC/OS is installed and you have deployed at least one private agent node.

### For machines that are currently private agents:
1.  Uninstall the current DC/OS software on the agent node.

        $ sudo -i /opt/mesosphere/bin/pkgpanda uninstall
        $ sudo systemctl stop dcos-mesos-slave
        $ sudo systemctl disable dcos-mesos-slave

1.  Remove the old directory structures on the agent node.

        $ sudo rm -rf /opt/mesosphere /var/lib/mesos

### Install DC/OS

1.  Copy the `dcos-install.tar` file created in the GUI or CLI installation method to the machine that will be a public agent.

        $ ssh $ROOT_USER@$AGENT "sudo mkdir scp ~/dcos-install.tar $ROOT_USER@$AGENT:/opt/dcos_install_tmp/dcos-install.tar

1.  SSH to the machine:

        $ ssh $ROOT_USER@$AGENT

1.  Install DC/OS as a public agent:

        $ sudo bash /opt/dcos_install_tmp/dcos_install.sh slave_public

1.  Verify that your new agent node is public by running this command from DC/OS CLI.

        $ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)/mesos/master/slaves | jq '.slaves[] | .reserved_resources' | grep slave_public | wc -l

    You should see an output greater than zero to indicate at least one public agent.
