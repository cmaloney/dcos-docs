---
layout: page
post_title: CLI Installer
menu_order: 0
---

<p>The automated CLI installer provides a guided installation of DC/OS from the command line. With this method you can choose from the complete set of DC/OS configuration options.</p>

<p>This installation method uses a bootstrap node to administer the DC/OS installation across your cluster. The bootstrap node uses an SSH key to connect to each node in your cluster to automate the DC/OS installation.</p>

<p>The DC/OS installation creates these folders:</p>

<ul>
<li><dl>
<dt><code>/opt/mesosphere</code></dt>
<dd>Contains all the DC/OS binaries, libraries, cluster configuration. Do not modify.</dd>
</dl></li>
<li><dl>
<dt><code>/etc/systemd/system/dcos.target.wants</code></dt>
<dd>Contains the systemd services which start the things that make up systemd. They must live outside of <code>/opt/mesosphere</code> because of systemd constraints.</dd>
</dl></li>
<li><dl>
<dt>Various units prefixed with <code>dcos</code> in <code>/etc/systemd/system</code></dt>
<dd>Copies of the units in <code>/etc/systemd/system/dcos.target.wants</code>. They must be at the top folder as well as inside <code>dcos.target.wants</code>.</dd>
</dl></li>
</ul>

<h2>Prerequisites</h2>

<p>Before installing DC/OS, your cluster must have the software and hardware <a href="/administration/installing/custom-1-7/system-requirements/">requirements</a>.</p>

<h1>Create an IP detection script</h1>

<p>In this step you create an IP detect script to broadcast the IP address of each node across the cluster. Each node in a DC/OS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DC/OS is started on the node.</p>

<p><strong>Important:</strong> The IP address of a node must not change after DC/OS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be <a href="/concepts/installing/custom/dcos-cleanup-script/">wiped and reinstalled</a>.</p>

<ol>
<li><p>Create a directory named <code>genconf</code> on your bootstrap node and navigate to it.</p>

<pre><code>$ mkdir -p genconf
</code></pre></li>
<li><p>Create an IP detection script for your environment and save as <code>genconf/ip-detect</code>. You can use the examples below.</p>

<ul>
<li><h4>Use the AWS Metadata Server</h4>

<p>This method uses the AWS Metadata service to get the IP address:</p>

<pre><code>#!/bin/sh
# Example ip-detect script using an external authority
# Uses the AWS Metadata Service to get the node's internal
# ipv4 address
curl -fsSL http://169.254.169.254/latest/meta-data/local-ipv4
</code></pre></li>
<li><h4>Use the GCE Metadata Server</h4>

<p>This method uses the GCE Metadata Server to get the IP address:</p>

<pre><code>#!/bin/sh
# Example ip-detect script using an external authority
# Uses the GCE metadata server to get the node's internal
# ipv4 address

curl -fsSl -H "Metadata-Flavor: Google" http://169.254.169.254/computeMetadata/v1/instance/network-interfaces/0/ip
</code></pre></li>
<li><h4>Use the IP address of an existing interface</h4>

<p>This method discovers the IP address of a particular interface of the node.</p>

<p>If you have multiple generations of hardware with different internals, the interface names can change between hosts. The IP detection script must account for the interface name changes. The example script could also be confused if you attach multiple IP addresses to a single interface, or do complex Linux networking, etc.</p>

<pre><code>#!/usr/bin/env bash
set -o nounset -o errexit
export PATH=/usr/sbin:/usr/bin:$PATH
echo $(ip addr show eth0 | grep -Eo '[0-9]{1,3}&#092;.[0-9]{1,3}&#092;.[0-9]{1,3}&#092;.[0-9]{1,3}' | head -1)
</code></pre></li>
<li><h4>Use the network route to the Mesos master</h4>

<p>This method uses the route to a Mesos master to find the source IP address to then communicate with that node.</p>

<p>In this example, we assume that the Mesos master has an IP address of <code>172.28.128.3</code>. You can use any language for this script. Your Shebang line must be pointed at the correct environment for the language used and the output must be the correct IP address.</p>

<pre><code>#!/usr/bin/env bash
set -o nounset -o errexit

MASTER_IP=172.28.128.3

echo $(/usr/sbin/ip route show to match 172.28.128.3 | grep -Eo '[0-9]{1,3}&#092;.[0-9]{1,3}&#092;.[0-9]{1,3}&#092;.[0-9]{1,3}' | tail -1)
</code></pre></li>
</ul></li>
</ol>

<h1><a name="config-json"></a>Create a configuration file</h1>

<p>In this step you create a YAML configuration file that is customized for your environment. DC/OS uses this configuration file during installation to generate your cluster installation files. In these instructions we assume that you are using ZooKeeper for shared storage.</p>

<ol>
<li><p>From your home directory, run this command to create a hashed password for superuser authentication, where <code>&lt;superuser_password&gt;</code> is the superuser password. Use the hashed password key for the <code>superuser_password_hash</code> parameter in your <code>config.yaml</code> file.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --hash-password &lt;superuser_password&gt;
</code></pre>

<p>Here is an example of a hashed password output.</p>

<pre><code>Extracting image from this script and loading into docker daemon, this step can take a few minutes
dcos-genconf.9eda4ae45de5488c0c-c40556fa73a00235f1.tar
Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
00:42:10 dcos_installer.action_lib.prettyprint:: ====&gt; HASHING PASSWORD TO SHA512
00:42:11 root:: Hashed password for 'password' key:
$6$rounds=656000$v55tdnlMGNoSEgYH$1JAznj58MR.Bft2wd05KviSUUfZe45nsYsjlEl84w34pp48A9U2GoKzlycm3g6MBmg4cQW9k7iY4tpZdkWy9t1   
</code></pre></li>
<li><p>Create a configuration file and save as <code>genconf/config.yaml</code>.</p>

<p>You can use this template to get started. This template specifies 3 Mesos masters, 5 Mesos agents, static master discovery list, and SSH configuration specified. If your servers are installed with a domain name in your <code>/etc/resolv.conf</code>, you should add <code>dns_search</code> to your <code>config.yaml</code> file. For parameters descriptions and configuration examples, see the <a href="/concepts/installing/custom/configuration-parameters/">documentation</a>.</p>

<pre><code>agent_list:
- &lt;agent-private-ip-1&gt;
- &lt;agent-private-ip-2&gt;
- &lt;agent-private-ip-3&gt;
- &lt;agent-private-ip-4&gt;
- &lt;agent-private-ip-5&gt;
# Use this bootstrap_url value unless you have moved the DC/OS installer assets.   
bootstrap_url: file:///opt/dcos_install_tmp
cluster_name: &lt;cluster-name&gt;
master_discovery: static 
master_list:
- &lt;master-private-ip-1&gt;
- &lt;master-private-ip-2&gt;
- &lt;master-private-ip-3&gt;
resolvers:
- 8.8.4.4
- 8.8.8.8 
ssh_port: 22
ssh_user: &lt;username&gt;
superuser_password_hash: &lt;hashed-password&gt;
superuser_username: &lt;username&gt;
</code></pre>

<p><strong>Important:</strong> You cannot use an NFS mount for Exhibitor storage with the automated command line installation method. To use an NFS mount for Exhibitor storage (<code>exhibitor_storage_backend: shared_filesystem</code>), you must use the <a href="/concepts/installing/custom/advanced/">Manual command line installation method</a>.</p></li>
<li><p>Copy your private SSH key to <code>genconf/ssh_key</code>. For more information, see the <a href="/concepts/installing/custom/configuration-parameters/">ssh_key_path</a> parameter.</p>

<pre><code>$ cp &lt;path-to-key&gt; genconf/ssh_key &amp;&amp; chmod 0600 genconf/ssh_key
</code></pre></li>
</ol>

<h1><a name="install-bash"></a>Install DC/OS</h1>

<p>In this step you create a custom DC/OS build file on your bootstrap node and then install DC/OS across your cluster nodes with SSH. With this installation method you create a bootstrap server that uses your SSH key and connects to every node to automate the deployment.</p>

<p>You can view all of the automated command line installer options with the <code>--help</code> flag:</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --help
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
</code></pre>

<p><strong>Tip:</strong> If something goes wrong and you want to rerun your setup, use these cluster <a href="/administration/installing/custom/dcos-cleanup-script/" target="_blank">cleanup instructions</a>.</p>

<p>To install DC/OS:</p>

<ol>
<li><p>From your home directory, run the DC/OS installer shell script on your bootstrapping master nodes to generate a customized DC/OS build. The setup script extracts a Docker container that uses the generic DC/OS install files to create customized DC/OS build files for your cluster. The build files are output to <code>./genconf/serve/</code>.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --genconf
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Extracking docker container from this script
dcos-genconf.4543c7745c7e-2af26a89fa52-cb932597d7b992.tar
Loading container into Docker daemon
...
</code></pre>

<p>At this point your directory structure should resemble:</p>

<pre><code>├── dcos-genconf.c9722490f11019b692-cb6b6ea66f696912b0.tar
├── dcos_generate_config.ee.sh
├── genconf
│   ├── config.yaml
│   ├── ip-detect     
</code></pre></li>
<li><p><a name="two"></a>Install the cluster prerequisites, including system updates, compression utilities (UnZip, GNU tar, and XZ Utils), and cluster permissions. For a full list of cluster prerequisites, see this <a href="/concepts/installing/custom/advanced/#scrollNav-2">documentation</a>.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --install-prereqs
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf 
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; EXECUTING INSTALL PREREQUISITES
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; START install_prereqs
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE install_prereqs
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE install_prereqs
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; END install_prereqs with returncode: 0
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; SUMMARY
====&gt; dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed install_prereqs stage.
</code></pre></li>
<li><p>Run a preflight script to validate that your cluster is installable.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --preflight
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; EXECUTING PREFLIGHT
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; START run_preflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE preflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE preflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE preflight_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE preflight_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; END run_preflight with returncode: 0
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; SUMMARY
====&gt; dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed run_preflight stage.
</code></pre>

<p><strong>Tip:</strong> For a detailed view, you can append log level debug (<code>-v</code>) to your command. For example <code>sudo bash dcos_generate_config.ee.sh --preflight -v</code>.</p></li>
<li><p>Install DC/OS on your cluster.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --deploy
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; EXECUTING DC/OS INSTALLATION
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; START deploy_master
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE deploy_master
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE deploy_master_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; END deploy_master with returncode: 0
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; SUMMARY
====&gt; dcos_installer.action_lib.prettyprint:: 1 out of 1 hosts successfully completed deploy_master stage.
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; START deploy_agent
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE deploy_agent
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE deploy_agent_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; END deploy_agent with returncode: 0
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; SUMMARY
====&gt; dcos_installer.action_lib.prettyprint:: 1 out of 1 hosts successfully completed deploy_agent stage.
</code></pre></li>
<li><p>Run the DC/OS diagnostic script to verify that services are up and running.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --postflight
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; EXECUTING POSTFLIGHT
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; START run_postflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE postflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE postflight
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE postflight_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; STAGE postflight_cleanup
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; END run_postflight with returncode: 0
====&gt; dcos_installer.action_lib.prettyprint:: ====&gt; SUMMARY
====&gt; dcos_installer.action_lib.prettyprint:: 2 out of 2 hosts successfully completed run_postflight stage.
</code></pre></li>
<li><p>Monitor Exhibitor and wait for it to converge at <code>http://&lt;master-public-ip&gt;:8181/exhibitor/v1/ui/index.html</code>.</p>

<p><strong>Tip:</strong> This process can take about 10 minutes. During this time you will see the Master nodes become visible on the Exhibitor consoles and come online, eventually showing a green light.</p>

<p><img src="/assets/images/chef-zk-status.png" alt="alt text" /></p>

<p>When the status icons are green, you can access the DC/OS web interface.</p></li>
<li><p>Launch the DC/OS web interface at: <code>http://&lt;public-master-ip&gt;/</code>.</p></li>
<li><p>Click <strong>Begin Installation</strong>. <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-begin-ee.gif" rel="attachment wp-att-4820"><img src="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-begin-ee-800x439.gif" alt="gui-installer-begin-ee" width="800" height="439" class="alignnone size-large wp-image-4820" /></a></p></li>
<li><p>Enter your administrator username and password and Click <strong>Log In To DC/OS</strong>.</p>

<p><img src="/assets/images/gui-installer-login-ee.gif" alt="alt text" /></p>

<p>You are done!</p>

<p><img src="/assets/images/ui-dashboard-ee.png" alt="alt text" /></p></li>
</ol>

<h1>Next Steps</h1>

<h3>Add DC/OS users</h3>

<p>You can assign user roles and grant access to DC/OS services. For more information, see the <a href="/administration/security-and-authentication/managing-authorization/">documentation</a>.</p>

<h3>Add more agent nodes</h3>

<p>After DC/OS is installed and deployed across your cluster, you can add more agent nodes.</p>

<p><strong>Prerequisite:</strong></p>

<ul>
<li>The agent nodes must meet the <a href="#hardware">hardware</a> and <a href="#software">software</a> prerequisites.</li>
</ul>

<ol>
<li>Update the <code>config.yaml</code> file with the additional agent nodes. For parameters descriptions and configuration examples, see the <a href="/concepts/installing/custom/configuration-parameters/">documentation</a>.</li>
<li><p>Run the installation steps beginning with <a href="#two">installing the cluster</a> prerequisites:</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --install-prereqs
</code></pre>

<p><strong>Important:</strong> You can ignore the errors that are shown. For example, during the <code>--preflight</code> you may see this error:</p>

<pre><code>====&gt; Found an existing DC/OS installation. To reinstall DC/OS on this this machine you must
====&gt; first uninstall DC/OS then run dcos_install.sh. To uninstall DC/OS, follow the product
====&gt; documentation provided with DC/OS.
====&gt;            
====&gt;  
====&gt; 10.10.0.160:22 FAILED
</code></pre></li>
</ol>

<h3>Uninstalling DC/OS</h3>

<ol>
<li><p>From the bootstrap node, enter this command:</p>

<pre><code>$ sudo bash dcos_generate_config.sh --uninstall
Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
====&gt; EXECUTING UNINSTALL
This will uninstall DC/OS on your cluster. You may need to manually remove /var/lib/zookeeper in some cases after this completes, please see our documentation for details. Are you ABSOLUTELY sure you want to proceed? [ (y)es/(n)o ]: yes
====&gt; START uninstall_dcos
====&gt; STAGE uninstall
====&gt; STAGE uninstall
====&gt; OUTPUT FOR uninstall_dcos
====&gt; END uninstall_dcos with returncode: 0
====&gt; SUMMARY FOR uninstall_dcos
2 out of 2 hosts successfully completed uninstall_dcos stage.
====&gt; END OF SUMMARY FOR uninstall_dcos
</code></pre></li>
</ol>
