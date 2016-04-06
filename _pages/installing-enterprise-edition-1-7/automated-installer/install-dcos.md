---
UID: 5703eac5c8a9f
post_title: Install DCOS
post_excerpt: ""
layout: page
published: true
menu_order: 2
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
**Important:** Encrypted SSH keys are not supported.

1.  From your terminal, start the DCOS GUI installer with this command.
    
        $ sudo bash dcos_generate_config.ee.sh --web
        
    
    Here is an example of the output.
    
        Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
        16:36:09 dcos_installer.action_lib.prettyprint:: ====> Starting DCOS installer in web mode
        16:36:09 root:: Starting server ('0.0.0.0', 9000)
        
    
    **Tip:** You can add the verbose (`-v`) flag to see the debug output:
    
        $ sudo bash dcos_generate_config.ee.sh --web -v
        

2.  Launch the DCOS web installer in your browser at: `http://<bootstrap-node-public-ip>:9000`.

3.  Click **Begin Installation**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-begin.png" rel="attachment wp-att-3190"><img src="/wp-content/uploads/2016/02/ui-installer-begin-800x510.png" alt="ui-installer-begin" width="800" height="510" class="alignnone size-large wp-image-3190" /></a>

4.  Specify your Deployment and DCOS Environment settings:
    
    ### Deployment Settings
    
    **Master Private IP List**
    :   Specify a comma-separated list of your internal static master IP addresses.
    
    **Agent Private IP List**
    :   Specify a comma-separated list of your internal static agent IP addresses.
    
    **Master Public IP**
    :   Specify a publicly accessible proxy IP address to one of your master nodes. If you don't have a proxy or already have access to the network where you are deploying this cluster, you can use one of the master IP's that you specified in the master list. This proxy IP address is used to access the DCOS web interface on the master node after DCOS is installed.
    
    **SSH Username**
    :   Specify the SSH username, for example `centos`. SSH Listening Port
    
    **SSH Listening Port**
    :   Specify the port to SSH to, for example `22`. SSH Key
    
    **SSH Key**
    :   Specify the private SSH key with access to your master IPs.
    
    ### DCOS Environment Settings
    
    **Username**
    :   Specify the administrator username. This username is required for using DCOS. This feature is only available in Enterprise Edition.
    
    **Password**
    :   Specify the administrator password. This password is required for using DCOS. This feature is only available in Enterprise Edition.
    
    **Upstream DNS Servers**
    
    :   Specify a comma-separated list of DNS resolvers for your DCOS cluster nodes. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them. If you have no internal hostnames to resolve, you can set this to a public nameserver like Google or AWS. In the example file above, the <a href="https://developers.google.com/speed/public-dns/docs/using" target="_blank">Google Public DNS IP addresses (IPv4)</a> are specified (`8.8.8.8` and `8.8.4.4`).
        
        *Caution:* If you set this parameter incorrectly you will have to reinstall DCOS. For more information about service discovery, see this [documentation][1].
    
    **IP Detect Script**
    
    :   Choose an IP detect script from the dropdown to broadcast the IP address of each node across the cluster. Each node in a DCOS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DCOS is started on the node.
        
        **Important:** The IP address of a node must not change after DCOS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be wiped and reinstalled.

5.  Click **Run Pre-Flight**. The preflight script installs the cluster [prerequisites][2] and validates that your cluster is installable. This step can take up to 15 minutes to complete. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-pre-flight1.png" rel="attachment wp-att-3197"><img src="/wp-content/uploads/2016/02/ui-installer-pre-flight1.png" alt="ui-installer-pre-flight1" width="626" height="405" class="alignnone size-full wp-image-3197" /></a>
    
    **Important:** If you exit your GUI installation before launching DCOS, you must do this before reinstalling:
    
        *   SSH to each node in your cluster and run `rm -rf /opt/mesosphere`.
        *   SSH to your bootstrap master node and run `rm -rf /var/lib/zookeeper`
        

6.  Click **Deploy** to install DCOS on your cluster. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-deploy1.png" rel="attachment wp-att-3195"><img src="/wp-content/uploads/2016/02/ui-installer-deploy1.png" alt="ui-installer-deploy1" width="628" height="406" class="alignnone size-full wp-image-3195" /></a>
    
    **Tip:** This step might take a few minutes, depending on the size of your cluster.

7.  Click **Run Post-Flight**. If errors any errors are found, fix and then click **Retry**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-post-flight1.png" rel="attachment wp-att-3196"><img src="/wp-content/uploads/2016/02/ui-installer-post-flight1.png" alt="ui-installer-post-flight1" width="623" height="366" class="alignnone size-full wp-image-3196" /></a>
    
    **Tip:** You can click **Download Logs** to view your logs locally.

8.  Click **Log In To DCOS**.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-success1.png" rel="attachment wp-att-3198"><img src="/wp-content/uploads/2016/02/ui-installer-success1.png" alt="ui-installer-success1" width="625" height="404" class="alignnone size-full wp-image-3198" /></a>

9.  Enter your administrator username and password.
    
    <a href="/wp-content/uploads/2016/02/ui-installer-auth2.png" rel="attachment wp-att-3341"><img src="/wp-content/uploads/2016/02/ui-installer-auth2-800x513.png" alt="ui-installer-auth2" width="800" height="513" class="alignnone size-large wp-image-3341" /></a>
    
    You are done!
    
    <a href="/wp-content/uploads/2016/02/ui-dashboard-ee.png" rel="attachment wp-att-3343"><img src="/wp-content/uploads/2016/02/ui-dashboard-ee-800x538.png" alt="ui-dashboard-ee" width="800" height="538" class="alignnone size-large wp-image-3343" /></a>

## Next Steps

Now you can [assign user roles][3].

 [1]: /administration/service-discovery/
 [2]: /installing-enterprise-edition-1-7/manual-installer/#scrollNav-2
 [3]: /administration/security-and-authentication/managing-authorization/