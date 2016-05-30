---
layout: page
post_title: Creating a Public Agent
menu_order: 8
---
<p>In DC/OS, agent nodes that are publicly accessible are designated as public and those that are not are designated as private. By default, agent nodes are designated as private during <a href="/administration/installing/custom/gui/">GUI</a> or <a href="/administration/installing/custom/cli/">CLI</a> installation.</p>

<p>You can determine how many public agent nodes are in your cluster by running the following command from the DC/OS CLI. A result of <code>0</code> indicates that you do not have a public agent. A result of <code>1</code> means that you have one or more public agents.</p>

<pre><code>$ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)/mesos/master/slaves | grep slave_public | wc -l
       0
</code></pre>

<p>These steps must be performed on a machine that is configured as a DC/OS node. Any tasks that are running on the node will be terminated during this conversion process.</p>

<h3>Prerequisites:</h3>

<ul>
<li>DC/OS is installed and you have deployed at least one master and one private agent node.</li>
<li>The archived DC/OS installer file (<code>dcos-install.tar</code>) from your <a href="/1.7/administration/installing/custom/gui/#backup">installation</a>. </li>
</ul>

<h3>Uninstall the DC/OS private agent software</h3>

<ol>
<li><p>Uninstall the current DC/OS software on the agent node.</p>

<pre><code>$ sudo -i /opt/mesosphere/bin/pkgpanda uninstall
$ sudo systemctl stop dcos-mesos-slave
$ sudo systemctl disable dcos-mesos-slave
</code></pre></li>
<li><p>Remove the old directory structures on the agent node.</p>

<pre><code>$ sudo rm -rf /etc/mesosphere /opt/mesosphere /var/lib/mesos
</code></pre></li>
<li><p>Restart the machine.</p>

<pre><code>$ sudo reboot
</code></pre></li>
</ol>

<h3>Install DC/OS and convert to a public agent node</h3>

<p>Copy the archived DC/OS installer file (<code>dcos-install.tar</code>) to the node that that is being converted to a public agent. This archive is created during the GUI or CLI <a href="/1.7/administration/installing/custom/gui/#backup">installation</a> method.</p>

<ol>
<li><p>Copy the files to your agent node. For example, you can use Secure Copy (scp) to copy <code>dcos-install.tar</code> to your home directory:</p>

<pre><code>$ scp ~/dcos-install.tar $username@$node-ip:~/dcos-install.tar
</code></pre></li>
<li><p>SSH to the machine:</p>

<pre><code>$ ssh $USER@$AGENT
</code></pre></li>
<li><p>Create a directory for the installer files:</p>

<pre><code>$ sudo mkdir -p /opt/dcos_install_tmp
</code></pre></li>
<li><p>Unpackage the <code>dcos-install.tar</code> file:</p>

<pre><code>$ sudo tar xf dcos-install.tar -C /opt/dcos_install_tmp
</code></pre></li>
<li><p>Install DC/OS as a public agent:</p>

<pre><code>$ sudo  /opt/dcos_install_tmp/dcos_install.sh slave_public
</code></pre></li>
<li><p>Verify that your new agent node is public by running this command from a workstation with the DC/OS CLI. You should see a result of <code>1</code>, which indicates that you have at least one public node.</p>

<pre><code>$ curl -skSL -H "Authorization: token=$(dcos config show core.dcos_acs_token)" $(dcos config show core.dcos_url)/mesos/master/slaves | grep slave_public | wc -l
</code></pre>

<p>1</p>

<p>You should see an output greater than zero to indicate at least one public agent.</p></li>
</ol>
