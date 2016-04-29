---
post_title: Converting a Private Node to Public  
layout: page
published: true
---


You can determine the agent node type by running this command from your terminal:

    curl -s http://<agent-public-ip>:5051/state | awk 'match($0,"default_role"){print substr($0,RSTART-1,29)}’ 
    
Public agent shows “default_role”:”slave_public”. Private agent shows as “default_role”:”slave_public”.


**Prerequisites:**

- You have completed DC/OS installation and have deployed a private agent node. 
- 


1.  From your bootstrap node, copy the `genconf/serve` directory to the agent node that you want to convert to public and run this command. <!-- do you need SSH access to your agent? -->

        $ scp -r ./genconf/serve <agent-private-ip>:/opt/dcos_install_tmp/

1.  SSH to your agent node: 

        $ ssh <agent-ip>
        
1.  Uninstall the current DC/OS software on the agent node.

        sudo -i /opt/mesosphere/bin/pkgpanda uninstall
        sudo systemctl stop dcos-mesos-slave
        sudo systemctl disable dcos-mesos-slave
        
1.  Remove the old directory structures on the agent node.
          
        sudo rm -rf /opt/mesosphere /var/lib/mesos


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