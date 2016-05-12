---
UID: 57239a7dd608a
post_title: GUI Installer
post_excerpt: ""
layout: page
published: true
menu_order: 2
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
The automated GUI installer provides a simple graphical interface that guides you through the installation of DC/OS. The automated installer provides a basic installation that is suitable for demonstrations and POCs. Only a subset of the configuration options are available with the GUI method. This is the fastest way to get started with DC/OS.

This installation method uses a bootstrap node to administer the DC/OS installation across your cluster. The bootstrap node uses an SSH key to connect to each node in your cluster to automate the DC/OS installation.

The DC/OS installation creates these folders:

*   `/opt/mesosphere`
    :   Contains all the DC/OS binaries, libraries, cluster configuration. Do not modify.

*   `/etc/systemd/system/dcos.target.wants`
    :   Contains the systemd services which start the things that make up systemd. They must live outside of `/opt/mesosphere` because of systemd constraints.

*   Various units prefixed with `dcos` in `/etc/systemd/system`
    :   Copies of the units in `/etc/systemd/system/dcos.target.wants`. They must be at the top folder as well as inside `dcos.target.wants`.
    
    ## Prerequisites
    
    Before installing DC/OS, your cluster must have the software and hardware [requirements][1].

# Install DC/OS

1.  From your terminal, start the DC/OS GUI installer with this command.
    
        $ sudo bash dcos_generate_config.ee.sh --web
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        16:36:09 dcos_installer.action_lib.prettyprint:: ====> Starting DC/OS installer in web mode
        16:36:09 root:: Starting server ('0.0.0.0', 9000)
        
    
    **Tip:** You can add the verbose (`-v`) flag to see the debug output:
    
        $ sudo bash dcos_generate_config.ee.sh --web -v
        

2.  Launch the DC/OS web installer in your browser at: `http://<bootstrap-node-public-ip>:9000`.

3.  Click **Begin Installation**. <a href="https://docs.mesosphere.com/wp-content/uploads/2016/04/gui-installer-begin-ee.gif" rel="attachment wp-att-4820"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/04/gui-installer-begin-ee-800x439.gif" alt="gui-installer-begin-ee" width="800" height="439" class="alignnone size-large wp-image-4820" /></a>

4.  Specify your Deployment and DC/OS Environment settings:
    
    ![alt text][2]
    
    ### Deployment Settings
    
    **Master Private IP List**
    :   Specify a comma-separated list of your internal static master IP addresses.
    
    **Agent Private IP List**
    :   Specify a comma-separated list of your internal static agent IP addresses.
    
    **Master Public IP**
    :   Specify a publicly accessible proxy IP address to one of your master nodes. If you don't have a proxy or already have access to the network where you are deploying this cluster, you can use one of the master IP's that you specified in the master list. This proxy IP address is used to access the DC/OS web interface on the master node after DC/OS is installed.
    
    **SSH Username**
    :   Specify the SSH username, for example `centos`.
    
    **SSH Listening Port**
    :   Specify the port to SSH to, for example `22`.
    
    **Private SSH Key**
    :   Specify the private SSH key with access to your master IPs.
    
    **Customer ID**
    :   Specify the 30-character UUID that was given to you by the Mesosphere customer representative.
    
    ### DC/OS Environment Settings
    
    **Username (Enterprise)**
    :   Specify the administrator username. This username is required for using DC/OS. This feature is only available in DC/OS Enterprise.
    
    **Password (Enterprise)**
    :   Specify the administrator password. This password is required for using DC/OS. This feature is only available in DC/OS Enterprise.
    
    **Upstream DNS Servers**
    
    :   Specify a comma-separated list of DNS resolvers for your DC/OS cluster nodes. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them. If you have no internal hostnames to resolve, you can set this to a public nameserver like Google or AWS. In the example file above, the <a href="https://developers.google.com/speed/public-dns/docs/using" target="_blank">Google Public DNS IP addresses (IPv4)</a> are specified (`8.8.8.8` and `8.8.4.4`). If Google DNS is not available in your country, you can replace the Google DNS servers `8.8.8.8` and `8.8.4.4` with your local DNS servers.
        
        *Caution:* If you set this parameter incorrectly you will have to reinstall DC/OS. For more information about service discovery, see this [documentation][3].
    
    **IP Detect Script**
    
    :   Choose an IP detect script from the dropdown to broadcast the IP address of each node across the cluster. Each node in a DC/OS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DC/OS is started on the node.
        
        **Important:** The IP address of a node must not change after DC/OS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be wiped and reinstalled.

5.  Click **Run Pre-Flight**. The preflight script installs the cluster prerequisites and validates that your cluster is installable. For a list of cluster prerequisites, see the [prerequisites][/administration/installing/custom/system-requirements/]. This step can take up to 15 minutes to complete. If errors any errors are found, fix and then click **Retry**.
    
    **Important:** If you exit your GUI installation before launching DC/OS, you must do this before reinstalling:
    
    *   SSH to each node in your cluster and run `rm -rf /opt/mesosphere`.
    *   SSH to your bootstrap master node and run `rm -rf /var/lib/zookeeper`

6.  Click **Deploy** to install DC/OS on your cluster. If errors any errors are found, fix and then click **Retry**.
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/04/ui-installer-deploy1.png" rel="attachment wp-att-4822"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/04/ui-installer-deploy1.png" alt="ui-installer-deploy1" width="628" height="406" class="alignnone size-full wp-image-4822" /></a>
    
    **Tip:** This step might take a few minutes, depending on the size of your cluster.

7.  Click **Run Post-Flight**. If errors any errors are found, fix and then click **Retry**.
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/04/ui-installer-post-flight1.png" rel="attachment wp-att-4821"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/04/ui-installer-post-flight1.png" alt="ui-installer-post-flight1" width="623" height="366" class="alignnone size-full wp-image-4821" /></a>
    
    **Tip:** You can click **Download Logs** to view your logs locally.

8.  Click **Log In To DC/OS**.
    
    <a href="https://docs.mesosphere.com/wp-content/uploads/2016/04/gui-installer-success-ee.gif" rel="attachment wp-att-4815"><img src="https://docs.mesosphere.com/wp-content/uploads/2016/04/gui-installer-success-ee-800x442.gif" alt="gui-installer-success-ee" width="800" height="442" class="alignnone size-large wp-image-4815" /></a>

9.  Enter your administrator username and password.
    
    ![alt text][5]
    
    You are done!
    
    ![alt text][6]

## Next Steps

Now you can [assign user roles][7].

### Uninstalling DC/OS

1.  From the bootstrap node, enter this command:
    
        $ sudo bash dcos_generate_config.sh --uninstall
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        ====> EXECUTING UNINSTALL
        This will uninstall DC/OS on your cluster. You may need to manually remove /var/lib/zookeeper in some cases after this completes, please see our documentation for details. Are you ABSOLUTELY sure you want to proceed? [ (y)es/(n)o ]: yes
        ====> START uninstall_dcos
        ====> STAGE uninstall
        ====> STAGE uninstall
        ====> OUTPUT FOR uninstall_dcos
        ====> END uninstall_dcos with returncode: 0
        ====> SUMMARY FOR uninstall_dcos
        2 out of 2 hosts successfully completed uninstall_dcos stage.
        ====> END OF SUMMARY FOR uninstall_dcos

 [1]: /administration/installing/custom-1-7/system-requirements/
 [2]: /assets/images/gui-installer-setup-ee.gif
 [3]: /usage/service-discovery/
 [4]: /scripted-installer/system-requirements/#scrollNav-2
 [5]: /assets/images/ui-installer-auth-1-7.gif
 [6]: /assets/images/dashboard-ee.gif
 [7]: /administration/security-and-authentication/managing-authorization/