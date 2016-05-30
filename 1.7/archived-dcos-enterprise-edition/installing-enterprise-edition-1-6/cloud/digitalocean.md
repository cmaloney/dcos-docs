---
layout: page
post_title: DigitalOcean DC/OS Installation Guide
menu_order: 0
---

<p>You can create a DC/OS cluster on DigitalOcean using Terraform.</p>

<p>The included Terraform templates are configured to run Mesosphere DC/OS on DigitalOcean. Depending on the DC/OS services that you install, or the amount of computing power your workload needs, you might have to modify the templates to suit your needs. You can modify the Terraform templates, but Mesosphere cannot assist in troubleshooting. If you require support with droplet creation or other related issues, please email support@digitalocean.com, visit the unofficial DigitalOcean IRC channel (#digitalocean on freenode) or consider the Enterprise DC/OS.</p>

<h2>Security</h2>

<ul>
<li>Keep in mind that all nodes are Internet-facing by default after deploying via Terraform and are not secured out-of-the-box. Additional configuration will be required to put master and agent nodes into a security group. </li>
</ul>

<h2>Environment</h2>

<ul>
<li><p>1, 3 or 5 Mesos master nodes in the admin zone</p></li>
<li><p>4 Mesos private agent nodes</p></li>
<li><p>1 Mesos public agent node</p></li>
<li><p>DigitalOcean 4GB (or more) Droplets</p></li>
</ul>

<h1>Create a DC/OS cluster</h1>

<h2>Prerequisites:</h2>

<ul>
<li><p><a href="https://www.digitalocean.com/help/api/">DigitalOcean API Key</a></p></li>
<li><p><a href="https://www.terraform.io/intro/getting-started/install.html">Terraform by Hashicorp</a></p></li>
</ul>

<h2>Installing DC/OS</h2>

<ol>
<li><p>Download and install Terraform using the instructions on the link provided above</p></li>
<li><p><a href="https://github.com/jmarhee/digitalocean-dcos-terraform">Download the DC/OS Terraform manifests from GitHub</a> into a local  directory.</p>

<pre><code>$ git clone https://github.com/jmarhee/digitalocean-dcos-terraform
</code></pre></li>
<li><p>From that directory, generate an ssh keypair:</p>

<pre><code>$ ssh-keygen -t rsa -f ./do-key
</code></pre></li>
<li><p>Copy <code>sample.terraform.tfvars</code> to a new file named <code>terraform.tfvars</code>, and edit the new file, filling in the values as desired. The following are blank and if not filled in, you will be prompted by terraform when necessary:</p>

<ul>
<li><p>digitalocean_token - Your DigitalOcean API key</p></li>
<li><p>ssh_key_fingerprint - Your SSH public key, or fingerprint</p></li>
<li><p>dcos_installer_url - Where to get DC/OS
https://downloads.dcos.io/dcos/EarlyAccess/dcos_generate_config.sh</p></li>
</ul>

<p>The following have default values and may be changed depending on your requirements:</p>

<ul>
<li><p>region - DigitalOcean facility: [NYC1|NYC2|NYC3|SGP1|LON1|AMS2|AMS3|SFO1|TOR1|FRA1]
Choose the DigitalOcean datacenter for your cluster - default NYC2</p></li>
<li><p>agent_size - Size of DigitalOcean Droplet to use for the DC/OS Agents: [4GB|8GB|16GB|32GB|48GB|64GB]
Choose the DigitalOcean droplet size to use for the DC/OS Private Agents - default 4GB</p></li>
<li><p>master_size - Size of DigitalOcean Droplet to use for the DC/OS Master: [4GB|8GB|16GB|32GB|48GB|64GB]
Choose the DigitalOcean droplet size to use for the DC/OS Master Nodes - default 4GB</p></li>
<li><p>boot_size - Size of DigitalOcean Droplet to use for the DC/OS Boot Node: [4GB|8GB|16GB|32GB|48GB|64GB]
Choose the DigitalOcean droplet size to use for the DC/OS Boot Server - default 4GB</p></li>
<li><p>dcos_cluster_name - the name of your DC/OS cluster - defaults to digitalocean-dcos</p></li>
<li><p>dcos_agent_count - Number of private agents to deploy - defaults to  4</p></li>
<li><p>dcos_public_agent_count - Number of public agents to deploy - defaults to 1</p></li>
<li><p>dcos_init_pubkey - The path to your ssh public key created in step 4 - defaults to ./do-key.pub</p></li>
<li><p>key_file_path - The path to your ssh private key created in step 4 - defaults to ./do-key</p></li>
</ul></li>
<li><p>Also from that same directory, run <code>terraform apply</code> which will deploy the servers into your project at DigitalOcean, and run the DC/OS installation routine. When it completes, you will see output similar to the following, but with the IP addresses assigned to your servers:</p>

<p><img src="/assets/images/digitalocean_terraform_output.png" alt="terraform apply output" /></p></li>
</ol>

<p>You may need to wait a few minutes from this point for all the DC/OS services to become active and the control panel available on the master node. After 15 or 20 minutes, you'll want to check out the <a href="../../custom/troubleshooting/">troubleshooting</a> documentation.</p>

<h1>Launch DC/OS</h1>

<p>Launch the DC/OS web interface by entering the Mesos master IP address:</p>

<ol>
<li><p>Cut/paste the link provided by after running terraform apply, or by running terraform output from the same directory, into your browser to open the DC/OS web interface. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname.</p></li>
<li><p>Install the DC/OS Command-Line Interface (CLI). You can install the CLI to administer your DC/OS cluster. You can access the documentation at any time by clicking the caret icon in the lower left corner.</p>

<p><img src="/assets/images/digitalocean_help_link.png" alt="dcos help link" /></p></li>
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
