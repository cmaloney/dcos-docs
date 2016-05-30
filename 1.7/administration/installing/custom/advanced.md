---
layout: page
post_title: Advanced Installer
menu_order: 4
---
<p>With this installation method, you package the DC/OS distribution yourself and connect to every node manually to run the DC/OS installation commands. This installation method is recommended if you want to integrate with an existing system or if you don’t have SSH access to your cluster.</p>

<p>The advanced installer requires:</p>

<ul>
<li>The bootstrap node must be network accessible from the cluster nodes.</li>
<li>The bootstrap node must have the HTTP(S) ports open from the cluster nodes.</li>
</ul>

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

<p>Before installing DC/OS, your cluster must have the software and hardware <a href="/administration/installing/custom/system-requirements/">requirements</a>.</p>

<h1>Create an IP detection script</h1>

<p>In this step you create an IP detect script to broadcast the IP address of each node across the cluster. Each node in a DC/OS cluster has a unique IP address that is used to communicate between nodes in the cluster. The IP detect script prints the unique IPv4 address of a node to STDOUT each time DC/OS is started on the node.</p>

<p><strong>Important:</strong> The IP address of a node must not change after DC/OS is installed on the node. For example, the IP address must not change when a node is rebooted or if the DHCP lease is renewed. If the IP address of a node does change, the node must be <a href="/administration/installing/custom/uninstall/">wiped and reinstalled</a>.</p>

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

curl -fsSL -H "Metadata-Flavor: Google" http://169.254.169.254/computeMetadata/v1/instance/network-interfaces/0/ip
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

<h1><a name="configuration"></a>Create a configuration file</h1>

<p>In this step you create a YAML configuration file that is customized for your environment. DC/OS uses this configuration file during installation to generate your cluster installation files. In these instructions we assume that you are using ZooKeeper for shared storage.</p>

<ol>
<li><p>From the bootstrap node, run this command to create a hashed password for superuser authentication, where <code>&lt;superuser_password&gt;</code> is the superuser password. Save the hashed password key for use in the <code>superuser_password_hash</code> parameter in your <code>config.yaml</code> file.</p>

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
<li><p>Create a configuration file and save as <code>genconf/config.yaml</code>. You can use this template to get started.</p>

<p>The template specifies 3 Mesos masters, static master discovery list, and Google DNS resolvers. If your servers are installed with a domain name in your <code>/etc/resolv.conf</code>, add the <code>dns_search</code> parameter. For parameters descriptions and configuration examples, see the <a href="/administration/installing/custom/uninstall/">documentation</a>.</p>

<p><strong>Tip:</strong> If Google DNS is not available in your country, you can replace the Google DNS servers <code>8.8.8.8</code> and <code>8.8.4.4</code> with your local DNS servers.</p>

<pre><code>bootstrap_url: http://&lt;bootstrap_public_ip&gt;:80      
cluster_name: '&lt;cluster-name&gt;'
master_discovery: static 
master_list:
- &lt;master-private-ip-1&gt;
- &lt;master-private-ip-2&gt;
- &lt;master-private-ip-3&gt;
resolvers:
- 8.8.4.4
- 8.8.8.8
superuser_password_hash: &lt;hashed-password&gt;
superuser_username: &lt;username&gt;
</code></pre></li>
<li><p>Optional: if you are using external volumes:</p>

<ul>
<li><p>Specify the <a href="/administration/installing/custom/configuration-parameters/#rexray-config"><code>rexray_config_method</code></a> parameter in your <code>genconf/config.yaml</code> file. For example:</p>

<pre><code>rexray_config_method: file
rexray_config_filename: path/to/rexray.yaml
</code></pre>

<p><strong>Tip:</strong> The <code>rexray_config_filename</code> path must be relative to your <code>genconf</code> directory.</p></li>
<li><p>Create a <code>genconf/rexray.yaml</code> file with your REX-Ray configuration specified. For example, here is a <code>rexray.yaml</code> file is configured for Amazon's EBS. Consult the <a href="http://rexray.readthedocs.io/en/stable/user-guide/config/">REX-Ray documentation</a> for more information.</p>

<pre><code>  rexray:
    loglevel: info
    storageDrivers:
      - ec2
    volume:
      unmount:
        ignoreusedcount: true
</code></pre></li>
</ul>

<p>For more information, see the external volumes <a href="/usage/services/marathon/external-volumes/">documentation</a>.</p></li>
</ol>

<h1><a name="install-bash"></a>Install DC/OS</h1>

<p>In this step you create a custom DC/OS build file on your bootstrap node and then install DC/OS onto your cluster. With this method you package the DC/OS distribution yourself and connect to every server manually and run the commands.</p>

<p><strong>Tip:</strong> If something goes wrong and you want to rerun your setup, use these cluster <a href="/administration/installing/custom/uninstall/">cleanup instructions</a>.</p>

<p><strong>Prerequisites</strong></p>

<ul>
<li>A <code>genconf/config.yaml</code> file that is optimized for manual distribution of DC/OS across your nodes.</li>
<li>A <code>genconf/ip-detect</code> script.</li>
</ul>

<!-- Early access URL: https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh -->

<p><!-- Stable URL: https://downloads.dcos.io/dcos/stable/dcos_generate_config.sh --> To install DC/OS:</p>

<ol>
<li><p>From the bootstrap node, run the DC/OS installer shell script to generate a customized DC/OS build file. The setup script extracts a Docker container that uses the generic DC/OS install files to create customized DC/OS build files for your cluster. The build files are output to <code>./genconf/serve/</code>.</p>

<p>At this point your directory structure should resemble:</p>

<pre><code>├── dcos-genconf.c9722490f11019b692-cb6b6ea66f696912b0.tar
├── dcos_generate_config.ee.sh
├── genconf
│   ├── config.yaml
│   ├── ip-detect
</code></pre></li>
<li><p>Run this command to generate your customized DC/OS build file:</p>

<pre><code>$ sudo bash dcos_generate_config.ee.sh
</code></pre>

<p><strong>Tip:</strong> For the install script to work, you must have created <code>genconf/config.yaml</code> and <code>genconf/ip-detect</code>.</p></li>
<li><p>From your home directory, run this command to host the DC/OS install package through an nginx Docker container. For <code>&lt;your-port&gt;</code>, specify the port value that is used in the <code>bootstrap_url</code>.</p>

<pre><code>$ sudo docker run -d -p &lt;your-port&gt;:80 -v $PWD/genconf/serve:/usr/share/nginx/html:ro nginx
</code></pre></li>
<li><p><A name="masterinstall"></A> Run these commands on each of your master nodes in succession to install DC/OS using your custom build file.</p>

<p><strong>Tip:</strong> Although there is no actual harm to your cluster, DC/OS may issue error messages until all of your master nodes are configured.</p>

<ol>
<li><p>SSH to your master nodes:</p>

<pre><code>$ ssh &lt;master-ip&gt;
</code></pre></li>
<li><p>Make a new directory and navigate to it:</p>

<pre><code>$ mkdir /tmp/dcos &amp;&amp; cd /tmp/dcos
</code></pre></li>
<li><p>Download the DC/OS installer from the nginx Docker container, where <code>&lt;bootstrap-ip&gt;</code> and <code>&lt;your_port&gt;</code> are specified in <code>bootstrap_url</code>:</p>

<pre><code>$ curl -O http://&lt;bootstrap-ip&gt;:&lt;your_port&gt;/dcos_install.sh
</code></pre></li>
<li><p>Run this command to install DC/OS on your master nodes:</p>

<pre><code>$ sudo bash dcos_install.sh master
</code></pre></li>
</ol></li>
<li><p><A name="slaveinstall"></A> Run these commands on each of your agent nodes to install DC/OS using your custom build file.</p>

<ol>
<li><p>SSH to your agent nodes:</p>

<pre><code>$ ssh &lt;agent-ip&gt;
</code></pre></li>
<li><p>Make a new directory and navigate to it:</p>

<pre><code>$ mkdir /tmp/dcos &amp;&amp; cd /tmp/dcos
</code></pre></li>
<li><p>Download the DC/OS installer from the nginx Docker container, where <code>&lt;bootstrap-ip&gt;</code> and <code>&lt;your_port&gt;</code> are specified in <code>bootstrap_url</code>:</p>

<pre><code>$ curl -O http://&lt;bootstrap-ip&gt;:&lt;your_port&gt;/dcos_install.sh
</code></pre></li>
<li><p>Run this command to install DC/OS on your agent nodes. You must designate your agent nodes as <a href="/overview/concepts/#public">public</a> or <a href="/overview/concepts/#private">private</a>.</p>

<ul>
<li><p>Private agent nodes:</p>

<pre>$ sudo bash dcos_install.sh slave</pre></li>
<li><p>Public agent nodes:</p>

<pre>$ sudo bash dcos_install.sh slave_public</pre></li>
</ul></li>
</ol></li>
<li><p>Monitor Exhibitor and wait for it to converge at <code>http://&lt;master-ip&gt;:8181/exhibitor/v1/ui/index.html</code>.</p>

<p><strong>Tip:</strong> This process can take about 10 minutes. During this time you will see the Master nodes become visible on the Exhibitor consoles and come online, eventually showing a green light.</p>

<p><img src="/assets/images/chef-zk-status.png" alt="alt text" /></p>

<p>When the status icons are green, you can access the DC/OS web interface.</p></li>
<li><p>Launch the DC/OS web interface at: <code>http://&lt;master-node-public-ip&gt;/</code>.</p></li>
<li><p>Enter your administrator username and password.</p>

<p><img src="/assets/images/ui-installer-auth2.png" alt="alt text" /></p>

<p>You are done!</p>

<p><img src="/assets/images/ui-dashboard-ee.png" alt="alt text" /></p></li>
</ol>

<h3>Next Steps</h3>

<p>Now you can <a href="/administration/security/">assign user roles</a>.</p>

<h3>Uninstalling DC/OS</h3>

<ol>
<li><p>Enter this command on each cluster node.</p>

<pre><code>$ sudo -i /opt/mesosphere/bin/pkgpanda uninstall &amp;&amp; sudo rm -rf /opt/mesosphere
</code></pre></li>
</ol>
