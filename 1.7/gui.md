---
layout: page
post_title: GUI Installer
menu_order: 0
---

<p>The automated GUI installer provides a simple graphical interface that guides you through the installation of DC/OS. The automated installer provides a basic installation that is suitable for demonstrations and POCs. Only a subset of the configuration options are available with the GUI method. This is the fastest way to get started with DC/OS.</p>

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
</dl>

<h2>Prerequisites</h2>

<p>Before installing DC/OS, your cluster must have the software and hardware <a href="/administration/installing/custom-1-7/system-requirements/">requirements</a>.</p></li>
</ul>

<h1>Install DC/OS</h1>

<ol>
<li><p>From your terminal, start the DC/OS GUI installer with this command.</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --web
</code></pre>

<p>Here is an example of the output.</p>

<pre><code>Running mesosphere/dcos-genconf docker with BUILD_DIR set to /home/centos/genconf
16:36:09 dcos_installer.action_lib.prettyprint:: ====&gt; Starting DC/OS installer in web mode
16:36:09 root:: Starting server ('0.0.0.0', 9000)
</code></pre>

<p><strong>Tip:</strong> You can add the verbose (<code>-v</code>) flag to see the debug output:</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh --web -v
</code></pre></li>
<li><p>Launch the DC/OS web installer in your browser at: <code>http://&lt;bootstrap-node-public-ip&gt;:9000</code>.</p></li>
<li><p>Click <strong>Begin Installation</strong>. <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-begin-ee.gif" rel="attachment wp-att-4820"><img src="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-begin-ee-800x439.gif" alt="gui-installer-begin-ee" width="800" height="439" class="alignnone size-large wp-image-4820" /></a></p></li>
<li><p>Specify your Deployment and DC/OS Environment settings:</p>

<p><img src="/assets/images/gui-installer-setup-ee.gif" alt="alt text" /></p>

<h3>Deployment Settings</h3>

<dl>
<dt><strong>Master Private IP List</strong></dt>
<dd>Specify a comma-separated list of your internal static master IP addresses.</dd>

<dt><strong>Agent Private IP List</strong></dt>
<dd>Specify a comma-separated list of your internal static agent IP addresses.</dd>

<dt><strong>Master Public IP</strong></dt>
<dd>Specify a publicly accessible proxy IP address to one of your master nodes. If you don't have a proxy or already have access to the network where you are deploying this cluster, you can use one of the master IP's that you specified in the master list. This proxy IP address is used to access the DC/OS web interface on the master node after DC/OS is installed.</dd>

<dt><strong>SSH Username</strong></dt>
<dd>Specify the SSH username, for example <code>centos</code>.</dd>

<dt><strong>SSH Listening Port</strong></dt>
<dd>Specify the port to SSH to, for example <code>22</code>.</dd>

<dt><strong>Private SSH Key</strong></dt>
<dd>Specify the private SSH key with access to your master IPs.</dd>

<dt><strong>Customer ID</strong></dt>
<dd>Specify the 30-character UUID that was given to you by the Mesosphere customer representative.</dd>
</dl>

<h3>DC/OS Environment Settings</h3>

<dl>
<dt><strong>Username (Enterprise)</strong></dt>
<dd>Specify the administrator username. This username is required for using DC/OS. This feature is only available in DC/OS Enterprise.</dd>

<dt><strong>Password (Enterprise)</strong></dt>
<dd>Specify the administrator password. This password is required for using DC/OS. This feature is only available in DC/OS Enterprise.</dd>

<dt><strong>Upstream DNS Servers</strong></dt>
<dd>
<p>Specify a comma-separated list of DNS resolvers for your DC/OS cluster nodes. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them. If you have no internal hostnames to resolve, you can set this to a public nameserver like Google or AWS. In the example file above, the <a href="https://developers.google.com/speed/public-dns/docs/using" target="_blank">Google Public DNS IP addresses (IPv4)</a> are specified (<code>8.8.8.8</code> and <code>8.8.4.4</code>).</p>

<p><em>Caution:</em> If you set this parameter incorrectly you will have to reinstall DC/OS. For more information about service discovery, see this <a href="/usage/service-discovery/">documentation</a>.</p>
</dd>

<dt><strong>IP Detect Script</strong></dt>
<dd>
<p>Choose an IP detect script from the dropdown to broadcast the IP address of each node across the cluster. Each node in a DC/OS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DC/OS is started on the node.</p>

<p><strong>Important:</strong> The IP address of a node must not change after DC/OS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be wiped and reinstalled.</p>
</dd>
</dl></li>
<li><p>Click <strong>Run Pre-Flight</strong>. The preflight script installs the cluster prerequisites and validates that your cluster is installable. For a list of cluster prerequisites, see the scripted installer <a href="/scripted-installer/system-requirements/#scrollNav-2">prerequisites</a>. This step can take up to 15 minutes to complete. If errors any errors are found, fix and then click <strong>Retry</strong>.</p>

<p><strong>Important:</strong> If you exit your GUI installation before launching DC/OS, you must do this before reinstalling:</p>

<ul>
<li>SSH to each node in your cluster and run <code>rm -rf /opt/mesosphere</code>.</li>
<li>SSH to your bootstrap master node and run <code>rm -rf /var/lib/zookeeper</code></li>
</ul></li>
<li><p>Click <strong>Deploy</strong> to install DC/OS on your cluster. If errors any errors are found, fix and then click <strong>Retry</strong>.</p>

<p><a href="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/ui-installer-deploy1.png" rel="attachment wp-att-4822"><img src="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/ui-installer-deploy1.png" alt="ui-installer-deploy1" width="628" height="406" class="alignnone size-full wp-image-4822" /></a></p>

<p><strong>Tip:</strong> This step might take a few minutes, depending on the size of your cluster.</p></li>
<li><p>Click <strong>Run Post-Flight</strong>. If errors any errors are found, fix and then click <strong>Retry</strong>.</p>

<p><a href="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/ui-installer-post-flight1.png" rel="attachment wp-att-4821"><img src="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/ui-installer-post-flight1.png" alt="ui-installer-post-flight1" width="623" height="366" class="alignnone size-full wp-image-4821" /></a></p>

<p><strong>Tip:</strong> You can click <strong>Download Logs</strong> to view your logs locally.</p></li>
<li><p>Click <strong>Log In To DC/OS</strong>.</p>

<p><a href="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-success-ee.gif" rel="attachment wp-att-4815"><img src="https://gitsync-mesosphere-documentation.pantheonsite.io/wp-content/uploads/2016/04/gui-installer-success-ee-800x442.gif" alt="gui-installer-success-ee" width="800" height="442" class="alignnone size-large wp-image-4815" /></a></p></li>
<li><p>Enter your administrator username and password.</p>

<p><img src="/assets/images/ui-installer-auth-1-7.gif" alt="alt text" /></p>

<p>You are done!</p>

<p><img src="/assets/images/dashboard-ee.gif" alt="alt text" /></p></li>
</ol>

<h2>Next Steps</h2>

<p>Now you can <a href="/administration/security-and-authentication/managing-authorization/">assign user roles</a>.</p>

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
