---
post_title: Converting a Private Node to Public  
layout: page
published: true
---

After installing DC/OS, you can designate private agent nodes as public. By default, agent nodes are designated as private during [GUI](/administration/installing/custom/gui/) or [CLI](/administration/installing/custom/cli/) installation. 

You can determine if your agent is public or private by running this command from DC/OS CLI:

    $ $ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)mesos/master/slaves | jq '.slaves[] | .reserved_resources' | grep slave_public | wc -l

An output of 1 means that you have a public agent. And output of 0 means that you do not have a public agent. For example, this query indicates no public agent:

    $ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)mesos/master/slaves | jq '.slaves[] | .reserved_resources' | grep slave_public | wc -l
    curl: (6) Could not resolve host: 52.39.210.95mesos
           0

    
**Prerequisites:**

- DC/OS is installed and you have deployed a private agent node. 

1.  From your bootstrap node, copy the `genconf/serve` directory to your workstation. 

        $ scp <ssh-username>@<agent-ip>:/path/to/install ~/
        
1.  Copy the directory to your private agent node.

        scp ~/install core@$AGENT:/dcos_install_tmp

1.  SSH to your agent node: 

        $ ssh <agent-ip>
        
1.  Uninstall the current DC/OS software on the agent node.

        sudo -i /opt/mesosphere/bin/pkgpanda uninstall
        sudo systemctl stop dcos-mesos-slave
        sudo systemctl disable dcos-mesos-slave
        
1.  Remove the old directory structures on the agent node.
          
        sudo rm -rf /opt/mesosphere /var/lib/mesos
        
1.  Indicate that you want this mesos agent node to have the Mesos role `slave_public`:

        $ mkdir -p /var/lib/dcos/
        
        $ echo "MESOS_DEFAULT_ROLE=slave_public" > /var/lib/dcos/mesos-slave-common
        
1.  Install the new agent software as a public agent node:

        $ sudo bash /opt/dcos_install_tmp/dcos_install.sh slave_public
        
1.  Verify that your agent node is publicby running this command from DC/OS CLI. 

        $ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)mesos/master/slaves | jq '.slaves[] | .reserved_resources' | grep slave_public | wc -l
    
    You should see an output of `1` to indicate public.
    
    


  # Run the following commands on the agent node that you want to be public (after you complete the GUI Installer procedure):
  # First, from the staging server, where you ran the DCOS GUI or Command Line Install program, copy the genconf/serve directory 
  # to the public agent node
  PUB_NODE=XXX.XXX.XXX.XXX
  scp -r ./genconf/serve $PUB_NODE:/opt/dcos_install_tmp/
  # SSH to the public agent node
  ssh $PUB_NODE
  # Uninstall the current DCOS software on the public agent node
  sudo -i /opt/mesosphere/bin/pkgpanda uninstall
  sudo systemctl stop dcos-mesos-slave
  sudo systemctl disable dcos-mesos-slave
  # Remove the old directory structures on the public agent node
  sudo rm -rf /opt/mesosphere /var/lib/mesos
  # Indicate that you want this mesos agent node to have the role slave_public
  mkdir -p /var/lib/dcos/
  echo "MESOS_DEFAULT_ROLE=slave_public" > /var/lib/dcos/mesos-slave-common
  # Install the new agent software as a public agent
  sudo bash /opt/dcos_install_tmp/dcos_install.sh slave_public
  # Test that the re-installed mesos agent is a slave_public (should see: “default_role”:”slave_public”
  curl -s http://$PUB_NODE:5051/state | awk 'match($0,"default_role"){print substr($0,RSTART-1,29)}’ 