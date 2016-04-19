---
UID: 56f984461f361
post_title: 'Step 4: Configure and install DC/OS (Minuteman)'
post_excerpt: ""
layout: page
published: true
menu_order: 105
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: true
hide_from_related: true
---
Choose your DC/OS installation method:

*   [Using the GUI installer to install DCOS][1]
*   [Using SSH to distribute DC/OS across your nodes][2]
*   [Manually distributing DC/OS across your nodes][3]

<!-- [Step 3: Configure and install DC/OS using SSH to distribute DC/OS across your nodes][5] -->

# <a name="gui"></a>Using the GUI installer to install DCOS

**Prerequisite:**

*   [ipsets][4]

1.  Run this command on all of your master IPs:
    
        $ mkdir -p /etc/mesosphere/roles/; touch /etc/mesosphere/roles/minuteman
        

2.  Download and save the DC/OS setup file, `dcos_generate_config.ee.sh`, to the `dcos` directory on your bootstrap node. This file is used to create your customized DC/OS build file.
    
    **Important:** Contact your sales representative or <sales@mesosphere.io> to obtain the DC/OS setup file.

3.  From your terminal, start the DC/OS installer with this command. Contact your sales representative or sales@mesosphere.io to obtain 1. From your terminal, start the DC/OS installer with this command.
    
    **Important:** Contact your sales representative or <sales@mesosphere.io> to obtain the DC/OS setup file.
    
        $ sudo bash dcos_generate_config.ee.sh --web
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        16:36:09 dcos_installer.action_lib.prettyprint:: ====> Starting DC/OS installer in web mode
        16:36:09 root:: Starting server ('0.0.0.0', 9000)
        
    
    You can add the verbose (`-v`) flag to see the debug output:
    
        $ sudo bash dcos_generate_config.ee.sh --web -v
        

4.  Launch the DC/OS web installer in your browser at: `http://<public-ip>:9000`.

5.  Click **Begin Installation**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-begin.png" rel="attachment wp-att-3190"><img src="/wp-content/uploads/2016/02/ui-installer-begin-800x510.png" alt="ui-installer-begin" width="800" height="510" class="alignnone size-large wp-image-3190" /></a>
    
    **Important:** If you exit your GUI installation before launching DCOS, you must do this before reinstalling:
    
    *   SSH to each node in your cluster and run `rm -rf /opt/mesosphere`.
    *   SSH to your bootstrap master node and run `rm -rf /var/lib/zookeeper`

6.  Specify your Deployment and DC/OS Environment settings:
    
    ### Deployment Settings
    
    **Master IP Address List**
    :   Specify a comma-separated list of your static master IP addresses.
    
    **Agent IP Address List**
    :   Specify a comma-separated list of your static agent IP addresses.
    
    **Accessible Master IP Address**
    :   Specify the IP address to a publicly accessible proxy to one of your master nodes. If you either don't have a proxy or you already have access to the network where you are deploying this cluster, you can use one of the master IP's that you specified in the master list. This proxy IP address is used to access the DC/OS web interface after the deployment succeeds.
    
    **SSH Username**
    :   Specify the SSH username, for example `centos`. SSH Listening Port
    :   Specify the port to SSH to, for example `22`. SSH Key
    :   Specify the SSH key with access to your master IPs.
    
    ### DC/OS Environment Settings
    
    **Username**
    :   Specify the administrator username. This username is required for using DCOS.
    
    **Password**
    :   Specify the administrator password. This password is required for using DCOS.
    
    **Bootstrapping Zookeeper IP Address(es)**
    :   Specify a comma-separated list of one or more Zookeeper node IP addresses to use for configuring the internal Exhibitor instances. Exhibitor uses this Zookeeper cluster to orchestrate it’s configuration.
    
    **Bootstrapping Zookeeper Port**
    :   Specify the Zookeeper port. For example, `2181`.
    
    **Upstream DNS Servers**
    :   Specify the DNS servers, which can be on your private network or the public internet. Caution: If you set this parameter incorrectly you will have to reinstall DCOS. For more information about service discovery, see this [documentation][4].
    
    **IP Detect Script**
    :   Specify an IP detect script to broadcast the IP address of each node across the cluster. For more information, see the [documentation][5].

7.  Click **Run Pre-Flight**. The preflight script validates that your cluster is installable. This step can take up to 15 minutes to complete. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-pre-flight1.png" rel="attachment wp-att-3197"><img src="/wp-content/uploads/2016/02/ui-installer-pre-flight1.png" alt="ui-installer-pre-flight1" width="626" height="405" class="alignnone size-full wp-image-3197" /></a>

8.  Click **Deploy** to install DC/OS on your cluster. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-deploy1.png" rel="attachment wp-att-3195"><img src="/wp-content/uploads/2016/02/ui-installer-deploy1.png" alt="ui-installer-deploy1" width="628" height="406" class="alignnone size-full wp-image-3195" /></a>

9.  Click **Run Post-Flight**. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-post-flight1.png" rel="attachment wp-att-3196"><img src="/wp-content/uploads/2016/02/ui-installer-post-flight1.png" alt="ui-installer-post-flight1" width="623" height="366" class="alignnone size-full wp-image-3196" /></a>
    
    **Tip:** You can click **Download Logs** to view your logs locally.

10. Click **Log In To DCOS**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-success1.png" rel="attachment wp-att-3198"><img src="/wp-content/uploads/2016/02/ui-installer-success1.png" alt="ui-installer-success1" width="625" height="404" class="alignnone size-full wp-image-3198" /></a>

11. Enter your administrator username and password.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-auth2.png" rel="attachment wp-att-3341"><img src="/wp-content/uploads/2016/02/ui-installer-auth2-800x513.png" alt="ui-installer-auth2" width="800" height="513" class="alignnone size-large wp-image-3341" /></a>
    
    You are done!
    
    <a href="/wp-content/uploads/2015/12/dashboardsmall.png" rel="attachment wp-att-1120"><img src="/wp-content/uploads/2015/12/dashboardsmall.png" alt="dashboardsmall" width="1338" height="828" class="alignnone size-full wp-image-1120" /></a>

### Next Steps

Now you can [assign user roles][6].

# <a name="ssh"></a>Using SSH to distribute DC/OS across your nodes

**Prerequisite:**

*   [ipsets][4]

### <a name="config-json"></a>4\.1 Configure your cluster

In this step you create a YAML configuration file that is customized for your environment. DC/OS uses this configuration file during installation to generate your cluster installation files. In these instructions we assume that you are using ZooKeeper for shared storage.

1.  Customize this `config.yaml` template file for your environment. <!-- do not change bootstrap_url -->
    
          cluster_name: '<cluster-name>'
          ##########################################
          # DO NOT CHANGE the bootstrap_url value, # 
          # unless you have moved installer assets.# 
          ##########################################
          bootstrap_url: file:///opt/dcos_install_tmp
          cluster_name: <cluster-name>
          exhibitor_storage_backend: zookeeper
          exhibitor_zk_hosts: <host1>:<port1>
          exhibitor_zk_path: /dcos
          log_directory: /genconf/logs
          master_discovery: static 
          master_list:
          - <master-ip-1>
          - <master-ip-2>
          - <master-ip-3>
          resolvers:
          - <dns-resolver-1>
          - <dns-resolver-2>
          ssh_port: '<port-number>'
          ssh_user: <username>
          superuser_password: <admin-username>
          superuser_username: <admin-password>
          agent_list:
          - <target-host-1>
          - <target-host-2>
          - <target-host-3>
          - <target-host-4>
          - <target-host-5>
        
    
    Specify these configuration parameters. <!-- log_directory: /genconf/logs, process_timeout: 120, ssh_key_path: /genconf/ssh-key -->
    
    **bootstrap_url**
    
    :   This parameter specifies the URI path for the DC/OS installer to store the customized DC/OS build files. Use the default value of `bootstrap_url: file:///opt/dcos_install_tmp`, unless you have moved the installer assets from their default location.
    
    **cluster_name**
    :   Specify the name of your cluster.
    
    **exhibitor_storage_backend**
    :   This parameter specifies the type of storage backend for Exhibitor. By default this is set to `zookeeper` in the `config.yaml` template file. During DC/OS installation, a storage system is required for configuring and orchestrating Zookeeper with Exhibitor on the master nodes. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DC/OS installation.
    
    **exhibitor_zk_hosts**
    :   Specify a comma-separated list of one or more Zookeeper node IP addresses to use for configuring the internal Exhibitor instances. Exhibitor uses this Zookeeper cluster to orchestrate it's configuration.
    
    **log_directory**
    :   This parameter specifies the path to the installer host logs from the SSH processes. By default this is set to `/genconf/logs`. This should not be changed because `/genconf` is local to the container that is running the installer, and is a mounted volume.
    
    **master_discovery**
    :   This parameter specifies the Mesos master discovery method. By default this is set to `static` in the `config.yaml` template file. The `static` method uses the Mesos agents to discover the masters by giving each agent a static list of master IPs. The masters must not change IP addresses, and if a master is replaced, the new master must take the old master's IP address
    
    **master_list**
    :   Specify a list of your static master IP addresses as a YAML nested series (`-`).
    
    **resolvers**
    
    :   Specify a JSON-formatted list of DNS servers for your DC/OS host nodes. You must include the escape characters (`\`) as shown in the template. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them.
        
        *Caution:* If you set the `resolvers` parameter incorrectly, you will permanently damage your configuration and have to reinstall DCOS.
    
    **ssh_port**
    :   This parameter specifies the port to SSH to, for example `22`.
    
    **ssh_user**
    :   This parameter specifies the SSH username, for example `centos`.
    
    **superuser_password**
    :   This parameter specifies the Admin password. This password is required for using DCOS.
    
    **superuser_username**
    :   This parameter specifies the Admin username. This username is required for using DCOS.
    
    **agent_list**
    :   This parameter specifies a complete list of IPv4 addresses to your agent host names. This must be a YAML-formatted nested series (-).
    
    For more configuration examples and all available options, see the [configuration file options][3].

2.  Save as `genconf/config.yaml`.

3.  Move your private SSH key to `genconf/ssh_key`. For more information, see the [ssh_key_path][5] parameter.
    
        $ cp <path-to-key> genconf/ssh_key && chmod 0600 genconf/ssh_key
        

### <a name="install-bash"></a>4\.2 Install DCOS

In this step you create a custom DC/OS build file on your bootstrap node and then install DC/OS onto your cluster. With this installation method you create a bootstrap server that uses your SSH key and connects to every node to automate the deployment.

**Tip:** If something goes wrong and you want to rerun your setup, use these cluster [cleanup instructions][7].

**Prerequisites**

*   A `genconf/config.yaml` file that is optimized for automatic distribution of DC/OS across your nodes with SSH.
*   A `genconf/ip-detect` script.

<!-- Early access URL: https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh -->

<!-- Stable URL: https://downloads.dcos.io/dcos/stable/dcos_generate_config.sh -->

To install DCOS:

1.  Download and save the DC/OS setup file, `dcos_generate_config.sh`, to the `dcos` directory on your bootstrap node. This file is used to create your customized DC/OS build file.
    
    **Important:** Contact your sales representative or <sales@mesosphere.io> to obtain the DC/OS setup file.

2.  From the `dcos` directory, run the DC/OS installer shell script on your bootstrapping master nodes to generate a customized DC/OS build. The setup script extracts a Docker container that uses the generic DC/OS install files to create customized DC/OS build files for your cluster. The build files are output to `./genconf/serve/`.
    
        $ sudo bash dcos_generate_config.sh --genconf
        Extracking docker container from this script
        dcos-genconf.4543c7745c7e-2af26a89fa52-cb932597d7b992.tar
        Loading container into Docker daemon
        ...
        
    
    At this point your directory structure should resemble:
    
        ├── dcos-genconf.c9722490f11019b692-cb6b6ea66f696912b0.tar
        ├── dcos_generate_config.sh
        ├── genconf
        │   ├── config.yaml
        │   ├── ip-detect
        

3.  Run this command on all of your master IPs:
    
        $ mkdir -p /etc/mesosphere/roles/; touch /etc/mesosphere/roles/minuteman
        

4.  Run a preflight script to validate that your cluster is installable.
    
        $ sudo bash dcos_generate_config.sh --preflight 
        Running mesosphere/dcos-genconf docker BUILD_DIR set to /home/someuser/genconf 
        ... 
        Running preflight checks
        

5.  Install DC/OS on your cluster.
    
        $ sudo bash dcos_generate_config.sh --deploy
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/someuser/genconf
        ...
        Starting DC/OS install process
        Running preflight checks
        Creating directories under /etc/mesosphere
        Creating role file for master
        Configuring DCOS
        Setting and starting DCOS
        
        Cleaning up temp directory /opt/dcos_install_tmp
        
    
    **Tip:** For a detailed view, you can add log level debug (`-v`) to your command. For example `sudo bash dcos_generate_config.sh --deploy -v`.

6.  Run the DC/OS diagnostic script to verify that services are up and running.
    
        $ sudo bash dcos_generate_config.sh --postflight
        

7.  Monitor Exhibitor and wait for it to converge at `http://<master-ip>:8181/exhibitor/v1/ui/index.html`.
    
    **Tip:** This process can take about 10 minutes. During this time you will see the Master nodes become visible on the Exhibitor consoles and come online, eventually showing a green light.
    
    <a href="/wp-content/uploads/2015/12/chef-zk-status.png" rel="attachment wp-att-2112"><img src="/wp-content/uploads/2015/12/chef-zk-status.png" alt="chef-zk-status" width="551" height="467" class="alignnone size-full wp-image-2112" /></a>
    
    When the status icons are green, you can access the DC/OS web interface.

8.  Launch the DC/OS web interface at: `http://<public-master-ip>/`.

9.  Click **Log In To DCOS**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-success1.png" rel="attachment wp-att-3198"><img src="/wp-content/uploads/2016/02/ui-installer-success1.png" alt="ui-installer-success1" width="625" height="404" class="alignnone size-full wp-image-3198" /></a>

10. Enter your administrator username and password.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-auth2.png" rel="attachment wp-att-3341"><img src="/wp-content/uploads/2016/02/ui-installer-auth2-800x513.png" alt="ui-installer-auth2" width="800" height="513" class="alignnone size-large wp-image-3341" /></a>
    
    You are done!
    
    <a href="/wp-content/uploads/2015/12/dashboardsmall.png" rel="attachment wp-att-1120"><img src="/wp-content/uploads/2015/12/dashboardsmall.png" alt="dashboardsmall" width="1338" height="828" class="alignnone size-full wp-image-1120" /></a>

### Next Steps

Now you can [assign user roles][6].

# <a name="manual"></a>Manually distributing DC/OS across your nodes

**Prerequisite:**

*   [ipsets][4]

### <a name="config-json"></a>4\.1 Configure your cluster

In this step you create a YAML configuration file that is customized for your environment. DC/OS uses this configuration file during installation to generate your cluster installation files. In these instructions we assume that you are using ZooKeeper for shared storage.

1.  Customize this `config.yaml` template file for your environment. <!-- bootstrap_url is changeable -->
    
          bootstrap_url: http://<bootstrap_ip>:<your_port>       
          cluster_name: '<cluster-name>'
          exhibitor_storage_backend: zookeeper
          exhibitor_zk_hosts: <host1>:<port1>
          exhibitor_zk_path: /dcos
          master_discovery: static 
          master_list:
          - <master-ip-1>
          - <master-ip-2>
          - <master-ip-3>
          resolvers:
          - <dns-resolver-1>
          - <dns-resolver-2>
          superuser_password: <admin-username>
          superuser_username: <admin-password>
        
    
    Specify these configuration parameters. <!-- log_directory: /genconf/logs, process_timeout: 120, ssh_key_path: /genconf/ssh-key -->
    
    **cluster_name**
    :   Specify the name of your cluster.
    
    **bootstrap_url**
    
    :   This parameter specifies the URI path for the DC/OS installer to store the customized DC/OS build files, which can be local (`bootstrap_url:file:///opt/dcos_install_tmp`) or hosted (`http://<your-web-server>`). By default this is set to `file:///opt/dcos_install_tmp` in the `config.yaml` template file, which is the location where the DC/OS installer puts your install tarball.
        
        **Tip:** This parameter is for advanced users. The default value should work for most installations.
    
    **exhibitor_storage_backend**
    :   This parameter specifies the type of storage backend for Exhibitor. By default this is set to `zookeeper` in the `config.yaml` template file. During DC/OS installation, a storage system is required for configuring and orchestrating Zookeeper with Exhibitor on the master nodes. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DC/OS installation.
    
    **exhibitor_zk_hosts**
    :   Specify a comma-separated list of one or more Zookeeper node IP addresses to use for configuring the internal Exhibitor instances. Exhibitor uses this Zookeeper cluster to orchestrate it's configuration.
    
    **master_discovery**
    :   This parameter specifies the Mesos master discovery method. By default this is set to `static` in the `config.yaml` template file. The `static` method uses the Mesos agents to discover the masters by giving each agent a static list of master IPs. The masters must not change IP addresses, and if a master is replaced, the new master must take the old master's IP address
    
    **master_list**
    :   Specify a list of your static master IP addresses as a YAML nested series (`-`).
    
    **resolvers**
    
    :   Specify a JSON-formatted list of DNS servers for your DC/OS host nodes. You must include the escape characters (`\`) as shown in the template. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them.
        
        *Caution:* If you set the `resolvers` parameter incorrectly, you will permanently damage your configuration and have to reinstall DCOS.
    
    **superuser_password**
    :   This parameter specifies the Admin password. This password is required for using DCOS.
    
    **superuser_username**
    :   This parameter specifies the Admin username. This username is required for using DCOS.
    
    For more configuration examples and all available options, see the [configuration file options][3].

2.  Save as `genconf/config.yaml`.

## <a name="install-bash"></a>4\.2 Install DCOS

In this step you create a custom DC/OS build file on your bootstrap node and then install DC/OS onto your cluster. With this method you package the DC/OS distribution yourself and connect to every server manually and run the commands.

**Tip:** If something goes wrong and you want to rerun your setup, use these cluster [cleanup instructions][5].

**Prerequisites**

*   A `genconf/config.yaml` file that is optimized for manual distribution of DC/OS across your nodes.
*   A `genconf/ip-detect` script. 
*   The Docker nginx image must be on your bootstrap node. You can use this command to install the nginx container:
    
         docker pull nginx
        
    
    For more information, see <a href="https://hub.docker.com/_/nginx/" target="_blank">DockerHub nginx</a> documentation.

<!-- Early access URL: https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh -->

<!-- Stable URL: https://downloads.dcos.io/dcos/stable/dcos_generate_config.sh --> To install DCOS:

1.  Download and save the DC/OS setup file, `dcos_generate_config.sh`, to the `dcos` directory on your bootstrap node. This file is used to create your customized DC/OS build file.
    
    **Important:** Contact your sales representative or <sales@mesosphere.io> to obtain the DC/OS setup file.

2.  From the `dcos` directory, run the DC/OS installer shell script on your bootstrapping master nodes to generate a customized DC/OS build. The setup script extracts a Docker container that uses the generic DC/OS install files to create customized DC/OS build files for your cluster. The build files are output to `./genconf/serve/`.
    
    At this point your directory structure should resemble:
    
        ├── dcos-genconf.c9722490f11019b692-cb6b6ea66f696912b0.tar
        ├── dcos_generate_config.sh
        ├── genconf
        │   ├── config.yaml
        │   ├── ip-detect
        

3.  Run this command to generate your customized DC/OS build file:
    
        $ sudo bash dcos_generate_config.sh
        
    
    **Tip:** For the install script to work, you must have created `genconf/config.yaml` and `genconf/ip-detect`.

4.  From the `dcos` directory, run this command to host the DC/OS install package through an nginx Docker container. For `<your-port>`, specify the port value that is used in the `bootstrap_url`.
    
        $ docker run -d -p <your-port>:80 -v $PWD/genconf/serve:/usr/share/nginx/html:ro nginx
        

5.  Run these commands on each of your master nodes in succession to install DC/OS using your custom build file.
    
    **Tip:** Although there is no actual harm to your cluster, DC/OS may issue error messages until all of your master nodes are configured.

6.  SSH to your node:
    
        $ ssh <master-ip>
        

7.  Make a new directory and navigate to it:
    
        $ mkdir /tmp/dcos && cd /tmp/dcos
        

8.  Download the DC/OS installer from the nginx Docker container, where `<bootstrap-ip>` and `<your_port>` are specified in `bootstrap_url`:
    
        $ curl -O http://<bootstrap-ip>:<your_port>/dcos_install.sh
        

9.  Run this command to install DC/OS on your master node:
    
        $ sudo bash dcos_install.sh master,minuteman
        

10. Run these commands on each of your agent nodes to install DC/OS using your custom build file.

11. SSH to your node:
    
        $ ssh <master-ip>
        

12. Make a new directory and navigate to it:
    
        $ mkdir /tmp/dcos && cd /tmp/dcos
        

13. Download the DC/OS installer from the nginx Docker container, where `<bootstrap-ip>` and `<your_port>` are specified in `bootstrap_url`:
    
        $ curl http://<bootstrap-ip>:<your_port>/dcos_install.sh
        

14. Run this command to install DC/OS on your agent node:
    
        $ sudo bash dcos_install.sh slave,minuteman
        

15. Monitor Exhibitor and wait for it to converge at `http://<master-ip>:8181/exhibitor/v1/ui/index.html`.
    
    **Tip:** This process can take about 10 minutes. During this time you will see the Master nodes become visible on the Exhibitor consoles and come online, eventually showing a green light.
    
    <a href="/wp-content/uploads/2015/12/chef-zk-status.png" rel="attachment wp-att-2112"><img src="/wp-content/uploads/2015/12/chef-zk-status.png" alt="chef-zk-status" width="551" height="467" class="alignnone size-full wp-image-2112" /></a>
    
    When the status icons are green, you can access the DC/OS web interface.

16. Launch the DC/OS web interface at: `http://<load-balanced-ip>/`.

17. Click **Log In To DCOS**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-success1.png" rel="attachment wp-att-3198"><img src="/wp-content/uploads/2016/02/ui-installer-success1.png" alt="ui-installer-success1" width="625" height="404" class="alignnone size-full wp-image-3198" /></a>

18. Enter your administrator username and password.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-auth2.png" rel="attachment wp-att-3341"><img src="/wp-content/uploads/2016/02/ui-installer-auth2-800x513.png" alt="ui-installer-auth2" width="800" height="513" class="alignnone size-large wp-image-3341" /></a>
    
    You are done!
    
    <a href="/wp-content/uploads/2015/12/dashboardsmall.png" rel="attachment wp-att-1120"><img src="/wp-content/uploads/2015/12/dashboardsmall.png" alt="dashboardsmall" width="1338" height="828" class="alignnone size-full wp-image-1120" /></a>

### Next Steps

Now you can [assign user roles][6].

 [1]: #ssh
 [2]: #manual
 [3]: /administration/installing/custom/configuration-parameters/
 [4]: http://ipset.netfilter.org/
 [5]: http://mesos.apache.org/documentation/latest/containerizer/
 [6]: /administration/security-and-authentication/managing-authorization/
 [7]: /administration/installing/custom/dcos-cleanup-script/