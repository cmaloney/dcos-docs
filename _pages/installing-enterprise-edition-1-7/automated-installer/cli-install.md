---
UID: 5708121e3f1ae
post_title: Command Line
post_excerpt: ""
layout: page
published: true
menu_order: 3
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
The automated command line installation method provides a guided installation of DCOS from the command line. With this method you can choose from the complete set of DCOS configuration options. 

# Create a Script for IP Address Discovery

In this step you create an IP detect script to broadcast the IP address of each node across the cluster. Each node in a DCOS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DCOS is started on the node.

**Important:** The IP address of a node must not change after DCOS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be [wiped and reinstalled][1].

1.  Create a directory named `genconf` on your bootstrap node and navigate to it.
    
        $ mkdir -p genconf
        

2.  Create an IP detection script for your environment and save as `genconf/ip-detect`. You can use the examples below.
    
    *   #### Use the AWS Metadata Server
        
        This method uses the AWS Metadata service to get the IP address:
        
            #!/bin/sh
            # Example ip-detect script using an external authority
            # Uses the AWS Metadata Service to get the node's internal
            # ipv4 address
            curl -fsSL http://169.254.169.254/latest/meta-data/local-ipv4
            
    
    *   #### Use the GCE Metadata Server
        
        This method uses the GCE Metadata Server to get the IP address:
        
            #!/bin/sh
            # Example ip-detect script using an external authority
            # Uses the GCE metadata server to get the node's internal
            # ipv4 address
            
            curl -fsSl -H "Metadata-Flavor: Google" http://169.254.169.254/computeMetadata/v1/instance/network-interfaces/0/ip
            
    
    *   #### Use the IP address of an existing interface
        
        This method discovers the IP address of a particular interface of the node.
        
        If you have multiple generations of hardware with different internals, the interface names can change between hosts. The IP detection script must account for the interface name changes. The example script could also be confused if you attach multiple IP addresses to a single interface, or do complex Linux networking, etc.
        
            #!/usr/bin/env bash
            set -o nounset -o errexit
            export PATH=/usr/sbin:/usr/bin:$PATH
            echo $(ip addr show eth0 | grep -Eo '[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}' | head -1)
            
    
    *   #### Use the network route to the Mesos master
        
        This method uses the route to a Mesos master to find the source IP address to then communicate with that node.
        
        In this example, we assume that the Mesos master has an IP address of `172.28.128.3`. You can use any language for this script. Your Shebang line must be pointed at the correct environment for the language used and the output must be the correct IP address.
        
            #!/usr/bin/env bash
            set -o nounset -o errexit
            
            MASTER_IP=172.28.128.3
            
            echo $(/usr/sbin/ip route show to match 172.28.128.3 | grep -Eo '[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}' | tail -1)
            

# <a name="config-json"></a>Configure your cluster

In this step you create a YAML configuration file that is customized for your environment. DCOS uses this configuration file during installation to generate your cluster installation files. In these instructions we assume that you are using ZooKeeper for shared storage.

1.  From your home directory, run this command to create a hashed password for superuser authentication, where `<superuser_password>` is the superuser password. Use the hashed password key for the `superuser_password_hash` parameter in your `config.yaml` file.
    
        $ sudo bash dcos_generate_config.ee.sh --hash-password <superuser_password>
        
    
    Here is an example of a hashed password output.
    
        Extracting image from this script and loading into docker daemon, this step can take a few minutes
        dcos-genconf.9eda4ae45de5488c0c-c40556fa73a00235f1.tar
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        00:42:10 dcos_installer.action_lib.prettyprint:: ====> HASHING PASSWORD TO SHA512
        00:42:11 root:: Hashed password for 'password' key:
        $6$rounds=656000$v55tdnlMGNoSEgYH$1JAznj58MR.Bft2wd05KviSUUfZe45nsYsjlEl84w34pp48A9U2GoKzlycm3g6MBmg4cQW9k7iY4tpZdkWy9t1   
        

2.  Create a configuration file and save as `genconf/config.yaml`.
    
    You can use this template to get started. This template specifies 3 Mesos masters, 5 Mesos agents, 3 ZooKeeper instances for Exhibitor storage, static master discovery list, and SSH configuration specified. If your servers are installed with a domain name in your `/etc/resolv.conf`, you should add `dns_search` to your `config.yaml` file. For parameters descriptions and configuration examples, see the [documentation][2].
    
        agent_list:
        - <agent-private-ip-1>
        - <agent-private-ip-2>
        - <agent-private-ip-3>
        - <agent-private-ip-4>
        - <agent-private-ip-5>
        # Use this bootstrap_url value unless you have moved the DCOS installer assets.   
        bootstrap_url: file:///opt/dcos_install_tmp
        cluster_name: <cluster-name>
        exhibitor_storage_backend: zookeeper
        exhibitor_zk_hosts: <host1>:2181,<host2>:2181,<host2>:2181
        exhibitor_zk_path: /dcos
        master_discovery: static 
        master_list:
        - <master-private-ip-1>
        - <master-private-ip-2>
        - <master-private-ip-3>
        resolvers:
        - 8.8.4.4
        - 8.8.8.8 
        ssh_port: 22
        ssh_user: <username>
        superuser_password_hash: <hashed-password>
        superuser_username: <username>
        
    
    **Important:** You cannot use an NFS mount for Exhibitor storage with the automated command line installation method. To use an NFS mount for Exhibitor storage (`exhibitor_storage_backend: shared_filesystem`), you must use the [Manual command line installation method][3].

3.  Copy your private SSH key to `genconf/ssh_key`. For more information, see the [ssh_key_path][2] parameter.
    
        $ cp <path-to-key> genconf/ssh_key && chmod 0600 genconf/ssh_key
        

# <a name="install-bash"></a>Install DCOS

In this step you create a custom DCOS build file on your bootstrap node and then install DCOS across your cluster nodes with SSH. With this installation method you create a bootstrap server that uses your SSH key and connects to every node to automate the deployment.

You can view all of the automated command line installer options with the `--help` flag:

    $ sudo bash dcos_generate_config.ee.sh --help
    Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
    usage: 
    Install Mesosophere's Data Center Operating System
    
    dcos_installer [-h] [-f LOG_FILE] [--hash-password HASH_PASSWORD] [-v]
    [--web | --genconf | --preflight | --deploy | --postflight | --uninstall | --validate-config | --test]
    
    Environment Settings:
    
      PORT                  Set the :port to run the web UI
      CHANNEL_NAME          ADVANCED - Set build channel name
      BOOTSTRAP_ID          ADVANCED - Set bootstrap ID for build
    
    optional arguments:
      -h, --help            show this help message and exit
      --hash-password HASH_PASSWORD
                            Hash a password on the CLI for use in the config.yaml.
      -v, --verbose         Verbose log output (DEBUG).
      --offline             Do not install preflight prerequisites on CentOS7,
                            RHEL7 in web mode
      --web                 Run the web interface.
      --genconf             Execute the configuration generation (genconf).
      --preflight           Execute the preflight checks on a series of nodes.
      --install-prereqs     Install preflight prerequisites. Works only on CentOS7
                            and RHEL7.
      --deploy              Execute a deploy.
      --postflight          Execute postflight checks on a series of nodes.
      --uninstall           Execute uninstall on target hosts.
      --validate-config     Validate the configuration in config.yaml
      --test                Performs tests on the dcos_installer application
    

**Tip:** If something goes wrong and you want to rerun your setup, use these cluster <a href="/administration/installing/custom/dcos-cleanup-script/" target="_blank">cleanup instructions</a>.

To install DCOS:

1.  From your home directory, run the DCOS installer shell script on your bootstrapping master nodes to generate a customized DCOS build. The setup script extracts a Docker container that uses the generic DCOS install files to create customized DCOS build files for your cluster. The build files are output to `./genconf/serve/`.
    
        $ sudo bash dcos_generate_config.ee.sh --genconf
        
    
    Here is an example of the output.
    
        Extracking docker container from this script
        dcos-genconf.4543c7745c7e-2af26a89fa52-cb932597d7b992.tar
        Loading container into Docker daemon
        ...
        
    
    At this point your directory structure should resemble:
    
        ├── dcos-genconf.c9722490f11019b692-cb6b6ea66f696912b0.tar
        ├── dcos_generate_config.ee.sh
        ├── genconf
        │   ├── config.yaml
        │   ├── ip-detect     
        

2.  <a name="two"></a>Install the cluster prerequisites, including system updates, compression utilities (UnZip, GNU tar, and XZ Utils), and cluster permissions. For a full list of cluster prerequisites, see this [documentation][4].
    
        $ sudo bash dcos_generate_config.ee.sh --install-prereqs
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf 
        20:47:49 dcos_installer.action_lib.prettyprint:: ====> EXECUTING INSTALL PREREQUISITES
        20:47:49 dcos_installer.action_lib.prettyprint:: ====> START install_prereqs
        20:52:32 dcos_installer.action_lib.prettyprint:: ====> STAGE install_prereqs
        20:52:55 dcos_installer.action_lib.prettyprint:: ====> STAGE install_prereqs
        20:52:55 dcos_installer.action_lib.prettyprint:: ====> END install_prereqs with returncode: 0
        20:52:55 dcos_installer.action_lib.prettyprint:: ====> SUMMARY
        20:52:55 dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed install_prereqs stage.
        

3.  Run a preflight script to validate that your cluster is installable.
    
        $ sudo bash dcos_generate_config.ee.sh --preflight
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        20:54:02 dcos_installer.action_lib.prettyprint:: ====> EXECUTING PREFLIGHT
        20:54:02 dcos_installer.action_lib.prettyprint:: ====> START run_preflight
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> STAGE preflight
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> STAGE preflight
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> STAGE preflight_cleanup
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> STAGE preflight_cleanup
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> END run_preflight with returncode: 0
        20:54:03 dcos_installer.action_lib.prettyprint:: ====> SUMMARY
        20:54:03 dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed run_preflight stage.
        
    
    **Tip:** For a detailed view, you can append log level debug (`-v`) to your command. For example `sudo bash dcos_generate_config.ee.sh --preflight -v`.

4.  Install DCOS on your cluster.
    
        $ sudo bash dcos_generate_config.ee.sh --deploy
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        20:55:00 dcos_installer.action_lib.prettyprint:: ====> EXECUTING DCOS INSTALLATION
        20:55:00 dcos_installer.action_lib.prettyprint:: ====> START deploy_master
        20:57:04 dcos_installer.action_lib.prettyprint:: ====> STAGE deploy_master
        20:57:04 dcos_installer.action_lib.prettyprint:: ====> STAGE deploy_master_cleanup
        20:57:04 dcos_installer.action_lib.prettyprint:: ====> END deploy_master with returncode: 0
        20:57:04 dcos_installer.action_lib.prettyprint:: ====> SUMMARY
        20:57:04 dcos_installer.action_lib.prettyprint:: 1 out of 1 hosts successfully completed deploy_master stage.
        20:57:04 dcos_installer.action_lib.prettyprint:: ====> START deploy_agent
        20:59:19 dcos_installer.action_lib.prettyprint:: ====> STAGE deploy_agent
        20:59:19 dcos_installer.action_lib.prettyprint:: ====> STAGE deploy_agent_cleanup
        20:59:19 dcos_installer.action_lib.prettyprint:: ====> END deploy_agent with returncode: 0
        20:59:19 dcos_installer.action_lib.prettyprint:: ====> SUMMARY
        20:59:19 dcos_installer.action_lib.prettyprint:: 1 out of 1 hosts successfully completed deploy_agent stage.
        

5.  Run the DCOS diagnostic script to verify that services are up and running.
    
        $ sudo bash dcos_generate_config.ee.sh --postflight
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        21:22:44 dcos_installer.action_lib.prettyprint:: ====> EXECUTING POSTFLIGHT
        21:22:44 dcos_installer.action_lib.prettyprint:: ====> START run_postflight
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> STAGE postflight
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> STAGE postflight
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> STAGE postflight_cleanup
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> STAGE postflight_cleanup
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> END run_postflight with returncode: 0
        21:22:45 dcos_installer.action_lib.prettyprint:: ====> SUMMARY
        21:22:45 dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed run_postflight stage.
        

6.  Monitor Exhibitor and wait for it to converge at `http://<master-public-ip>:8181/exhibitor/v1/ui/index.html`.
    
    **Tip:** This process can take about 10 minutes. During this time you will see the Master nodes become visible on the Exhibitor consoles and come online, eventually showing a green light.
    
    ![alt text](/assets/images/chef-zk-status.png)
    
    When the status icons are green, you can access the DCOS web interface.

7.  Launch the DCOS web interface at: `http://<public-master-ip>/`.

8.  Click **Log In To DCOS**.
    
    ![alt text](/assets/images/ui-installer-success1.png)

9.  Enter your administrator username and password.
    
    ![alt text](/assets/images/ui-installer-auth2.png)
    
    You are done!
    
    ![alt text](/assets/images/ui-dashboard-ee.png)

# Next Steps

### Add DCOS users

You can assign user roles and grant access to DCOS services. For more information, see the [documentation][5].

### Add more agent nodes

After DCOS is installed and deployed across your cluster, you can add more agent nodes.

**Prerequisite:**

*   The agent nodes must meet the [hardware][6] and [software][7] prerequisites.

1.  Update the `config.yaml` file with the additional agent nodes. For parameters descriptions and configuration examples, see the [documentation][2].
2.  Run the installation steps beginning with [installing the cluster][8] prerequisites:
    
        $ sudo bash dcos_generate_config.ee.sh --install-prereqs
        
    
    **Important:** You can ignore the errors that are shown. For example, during the `--preflight` you may see this error:
    
        18:17:14::           Found an existing DCOS installation. To reinstall DCOS on this this machine you must
        18:17:14::           first uninstall DCOS then run dcos_install.sh. To uninstall DCOS, follow the product
        18:17:14::           documentation provided with DCOS.
        18:17:14::           
        18:17:14:: 
        18:17:14:: ====> 10.10.0.160:22 FAILED

 [1]: /concepts/installing/custom/dcos-cleanup-script/
 [2]: /concepts/installing/custom/configuration-parameters/
 [3]: /concepts/installing/custom/manual-installation/
 [4]: /concepts/installing/custom/manual-installation/#scrollNav-2
 [5]: /administration/security-and-authentication/managing-authorization/
 [6]: #hardware
 [7]: #software
 [8]: #two