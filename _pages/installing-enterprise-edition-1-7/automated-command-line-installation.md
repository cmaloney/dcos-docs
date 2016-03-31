---
UID: 56f984450e872
post_title: Automated command line installation
post_excerpt: ""
layout: page
published: true
menu_order: 2
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: true
---
The automated command line installation method provides a guided installation of DCOS Enterprise Edition.

This installation method uses a bootstrap node to administer the DCOS installation across your cluster. The bootstrap node uses an SSH key to connect to each node in your cluster to automate the DCOS installation.

To use the automated command-line installation method:

*   Cluster nodes must be network accessible from the bootstrap node 
*   Cluster nodes must have SSH enabled and ports open from the bootstrap node
*   The bootstrap node must have an unencrypted SSH key that can be used to authenticate with the cluster nodes over SSH 

<a name="hardware"></a>

[installing-enterprise-edition-hardware]

<a name="software"></a>

[installing-enterprise-edition-software-ssh]

[installing-enterprise-edition-ip-detect]

# Step 2: Configure and Install DCOS

In this step you create a customized YAML configuration file and install DCOS across your cluster using SSH.

**Prerequisite:**

*   Encrypted SSH keys are not supported.

## <a name="config-json"></a>Configure your cluster

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
        

2.  Create a configuration file and save as `genconf/config.yaml`. For parameters descriptions and configuration examples, see the [documentation][1].
    
    You can use this template to get started. This template specifies 3 Mesos masters, 5 Mesos agents, 3 ZooKeeper instances for Exhibitor storage, static master discovery list, and SSH configuration specified.
    
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
        ssh_port: '22'
        ssh_user: <username>
        superuser_password_hash: <hashed-password>
        superuser_username: <username>
        
    
    **Important:** You cannot use an NFS mount for Exhibitor storage with the automated command line installation method. To use an NFS mount for Exhibitor storage (`exhibitor_storage_backend: shared_filesystem`), you must use the [Manual command line installation method][2].

3.  Copy your private SSH key to `genconf/ssh_key`. For more information, see the [ssh_key_path][1] parameter.
    
        $ cp <path-to-key> genconf/ssh_key && chmod 0600 genconf/ssh_key
        

## <a name="install-bash"></a>Install DCOS

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
    

**Tip:** If something goes wrong and you want to rerun your setup, use these cluster <a href="https://docs.mesosphere.com/getting-started/installing/installing-enterprise-edition/dcos-cleanup-script/" target="_blank">cleanup instructions</a>.

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
        │   ├── cluster_packages.json
        │   ├── config.yaml
        │   ├── ip-detect    
        │   ├── serve
        │   ├── ssh_key
        │   ├── state 
        

2.  <a name="two"></a>Install the cluster prerequisites, including system updates, compression utilities (UnZip, GNU tar, and XZ Utils), and cluster permissions. For a full list of cluster prerequisites, see this [documentation][3].
    
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
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2015/12/chef-zk-status.png" rel="attachment wp-att-2112"><img src="https://docs.mesosphere.com/wp-content/uploads/2015/12/chef-zk-status.png" alt="chef-zk-status" width="551" height="467" class="alignnone size-full wp-image-2112" /></a>
    
    When the status icons are green, you can access the DCOS web interface.

7.  Launch the DCOS web interface at: `http://<public-master-ip>/`.

8.  Click **Log In To DCOS**.
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-installer-success1.png" rel="attachment wp-att-3198"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-installer-success1.png" alt="ui-installer-success1" width="625" height="404" class="alignnone size-full wp-image-3198" /></a>

9.  Enter your administrator username and password.
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-installer-auth2.png" rel="attachment wp-att-3341"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-installer-auth2-800x513.png" alt="ui-installer-auth2" width="800" height="513" class="alignnone size-large wp-image-3341" /></a>
    
    You are done!
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-dashboard-ee.png" rel="attachment wp-att-3343"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/02/ui-dashboard-ee-800x538.png" alt="ui-dashboard-ee" width="800" height="538" class="alignnone size-large wp-image-3343" /></a>

# Next Steps

### Add DCOS users

You can assign user roles and grant access to DCOS services. For more information, see the [documentation][4].

### Add agent nodes

After DCOS is installed and deployed across your cluster, you can add more agent nodes.

**Prerequisite:**

*   The agent nodes must meet the [hardware][5] and [software][6] prerequisites.

1.  Update the `config.yaml` file with the additional agent nodes. For parameters descriptions and configuration examples, see the [documentation][1].
2.  Run the installation steps beginning with [installing the cluster][7] prerequisites:
    
        $ sudo bash dcos_generate_config.ee.sh --install-prereqs
        
    
    **Important:** You can ignore the errors that are shown. For example, during the `--preflight` you may see this error:
    
        18:17:14::           Found an existing DCOS installation. To reinstall DCOS on this this machine you must
        18:17:14::           first uninstall DCOS then run dcos_install.sh. To uninstall DCOS, follow the product
        18:17:14::           documentation provided with DCOS.
        18:17:14::           
        18:17:14:: 
        18:17:14:: ====> 10.10.0.160:22 FAILED

 [1]: ../configuration-parameters/
 [2]: ../manual-installation/
 [3]: ../manual-installation/#scrollNav-2
 [4]: ../security-and-authentication/managing-authorization/
 [5]: #hardware
 [6]: #software
 [7]: #two