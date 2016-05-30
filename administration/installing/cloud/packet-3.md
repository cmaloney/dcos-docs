---
layout: page
post_title: Packet DC/OS Installation Guide
menu_order: 0
---
<p>You can create a DCOS cluster on Packet bare metal using Terraform.</p>

<p>The included Terraform templates are configured to run Mesosphere DC/OS on Packet. Depending on the DC/OS services that you install, or the amount of computing power your workload needs, you might have to modify the templates to suit your needs. You can modify the Terraform templates, but Mesosphere cannot assist in troubleshooting. If you require support, please email help@packet.net, visit the Packet IRC channel (#packethost on freenode) or consider the Enterprise DC/OS.</p>

<h2>Hardware</h2>

<ul>
<li><p>1, 3 or 5 Mesos master nodes in the admin zone</p></li>
<li><p>4 Mesos private agent nodes</p></li>
<li><p>1 Mesos public agent node</p></li>
<li><p>Packet “Type 0” Server instances</p></li>
</ul>

<h1>Create a DCOS cluster</h1>

<h2>Prerequisites:</h2>

<ul>
<li><p><a href="https://www.packet.net/resources/kb/how-do-i-create-api-keys/">Packet API Key</a></p></li>
<li><p><a href="https://www.packet.net/preview/kb/where-do-i-locate-my-packet-project-id/">Packet Project ID</a></p></li>
<li><p><a href="https://www.terraform.io/intro/getting-started/install.html">Terraform by Hashicorp</a></p></li>
</ul>

<h2>Installing DCOS</h2>

<h4>With this method, the network is open by default. Because of this, network security is a concern and should be addressed as soon as possible by the administrator.</h4>

<ol>
<li><p>Download and install Terraform using the instructions on the link provided above</p></li>
<li><p><a href="https://github.com/dcos/packet-terraform">Download the DCOS Terraform manifests from GitHub</a> into a local directory.</p>

<pre><code>$ git clone https://github.com/dcos/packet-terraform
</code></pre></li>
<li><p>From that directory, generate an ssh keypair:</p>

<pre><code>$ ssh-keygen -t rsa -f ./packet-key
</code></pre></li>
<li><p>Copy <code>sample.terraform.tfvars</code> to a new file named <code>terraform.tfvars</code>, and edit the new file, filling in the values as desired. The following are blank and if not filled in, you will be prompted by terraform when necessary:</p>

<ul>
<li><p>packet_api_key - Your Packet API key</p></li>
<li><p>packet_project_id - Packet Project ID</p></li>
<li><p>dcos_installer_url - Where to get DCOS https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh</p></li>
</ul>

<p>The following have default values and may be changed depending on your requirements:</p>

<ul>
<li><p>packet_facility - Packet facility: [ewr1|sjc1|ams1] ewr1 is New Jersey, ams1 is Amsterdam, sjc1 is San Jose - default sjc1</p></li>
<li><p>packet_agent_type - Type of Packet Server to use for the DCOS Agents: [baremetal_0|baremetal_1|baremetal_3] Choose the Packet Server type to use for the DCOS Private Agents - default baremetal_0</p></li>
<li><p>packet_master_type - Type of Packet Server to use for the DCOS Master: [baremetal_0|baremetal_1|baremetal_3] Choose the Packet Server type to use for the DCOS Master Nodes - default baremetal_0</p></li>
<li><p>packet_boot_type - Type of Packet Server to use for the DCOS Boot Node: [baremetal_0|baremetal_1|baremetal_3] Choose the Packet Server type to use for the DCOS Boot Server - default baremetal_0</p></li>
<li><p>dcos_cluster_name - the name of your DCOS cluster - defaults to packet-dcos</p></li>
<li><p>dcos_agent_count - Number of private agents to deploy - defaults to 4</p></li>
<li><p>dcos_public_agent_count - Number of public agents to deploy - defaults to 1</p></li>
<li><p>dcos_init_pubkey - The path to your ssh public key created in step 4 - defaults to ./packet-key.pub</p></li>
<li><p>key_file_path - The path to your ssh private key created in step 4 - defaults to ./packet-key</p></li>
</ul></li>
<li><p>Also from that same directory, run <code>terraform apply</code> which will deploy the servers into your project at Packet, and run the DCOS installation routine. When it completes, you will see output similar to the following, but with the IP addresses assigned to your servers:</p>

<p><img src="/assets/images/packet_terraform_output.png" alt="terraform apply output" /></p></li>
</ol>

<p>You may need to wait a few minutes from this point for all the DCOS services to become active and the control panel available on the master node. After 15 or 20 minutes, you'll want to check out the <a href="../../custom/troubleshooting/">troubleshooting</a> documentation.</p>

<h1>Launch DCOS</h1>

<p>Launch the DCOS web interface by entering the Mesos master IP address:</p>

<ol>
<li><p>Cut/paste the link provided by after running terraform apply, or by running terraform output from the same directory, into your browser to open the DCOS web interface. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname.</p></li>
<li><p>Install the DCOS Command-Line Interface (CLI). You can install the CLI to administer your DCOS cluster. You can access the documentation at any time by clicking the caret icon in the lower left corner.</p>

<p><img src="/assets/images/packet_help_link.png" alt="dcos help link" /></p></li>
</ol>

<h2>Next steps</h2>

<ul>
<li><p>Adding and removing nodes:</p>

<ul>
<li><p>Run <code>terraform apply -var ‘dcos_agent_count=N’</code> to change the private agent count to the number specified. (<code>‘dcos_public_agent_count’</code> is also available)</p></li>
<li><p>Increasing node count is fast, safe, and fun!</p></li>
<li><p>We recommend against reducing the node count in production. Stateful DC/OS apps and services may suffer outages and failures if nodes are not put into maintenance mode, and their tasks rescheduled through their respective schedulers.</p></li>
</ul></li>
</ul>
