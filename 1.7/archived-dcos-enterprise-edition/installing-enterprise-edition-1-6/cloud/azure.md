---
layout: page
post_title: Install DC/OS on Azure
menu_order: 0
---

<p>This document explains how to install DC/OS by using the Azure Marketplace.</p>

<p>TIP: To get support on Azure Marketplace-related questions, join the Azure Marketplace <a href="http://join.marketplace.azure.com">Slack community</a>.</p>

<h1>System requirements</h1>

<h2>Hardware</h2>

<p>To <a href="/usage/">use</a> all of the services offered in DC/OS, you should choose at least five Mesos Agents using <code>Standard_D2</code> <a href="https://azure.microsoft.com/en-us/pricing/details/virtual-machines/">Virtual Machines</a>, which is the default size in the DC/OS Azure Marketplace offering.</p>

<p>Selecting smaller-sized VMs is not recommended, and selecting fewer VMs will likely cause certain resource-intensive services such as distributed datastores not to work properly (from installation issues to operational limitations).</p>

<h2>Software</h2>

<p>You will need an active <a href="https://azure.microsoft.com/en-us/pricing/purchase-options/">Azure subscription</a> to install DC/OS via the Azure Marketplace.</p>

<p>Also, to access nodes in the DC/OS cluster you will need <code>ssh</code> installed and configured.</p>

<h1>Install DC/OS</h1>

<h2>Step 1: Deploying the template</h2>

<p>To deploy DC/OS using an <a href="https://azure.microsoft.com/en-us/documentation/articles/resource-group-overview/">Azure Resource Manager</a> template, first go to <a href="https://portal.azure.com/">portal.azure.com</a>, click on <code>+ New</code> and enter <code>DC/OS</code>:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1a.png" alt="Searching for DC/OS template" /></p>

<p>In the search result page, pick <code>DC/OS on Azure</code>:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1b.png" alt="Selecting DC/OS template" /></p>

<p>In the template, click on <code>Create</code>:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1c.png" alt="Creating deployment using DC/OS template" /></p>

<p>Complete the installation wizard steps. Note: you are only required to fill in the <code>Basic</code> section, however it is strongly recommended that you create a new resource group (simplifies installation and cluster teardown):</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1d.png" alt="Filling in DC/OS template" /></p>

<p>After you've clicked on the final <code>Create</code> button you should see something like the screen below. The default 5 node configuration should take about 15 minutes to deploy.</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1e.png" alt="Deploying DC/OS template" /></p>

<p>After the deployment succeeded, click on the resource group (<code>mydcoscluster</code> here) and you should get to the resource group. If you don't see it, try searching for your research group and if the deployment failed, delete the deployment and the resource group and start again:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step1f.png" alt="DC/OS template successfully deployed" /></p>

<p>Congratulations, you have now deployed DC/OS by using an Azure Resource Manager template! Next we will access the cluster.</p>

<h2>Step 2: Accessing DC/OS</h2>

<p>Because of security considerations, the DC/OS cluster in Azure is locked down by default. You must use an <code>ssh</code> tunnel to access the DC/OS Dashboard.</p>

<p>First, look up <code>MASTERFQDN</code> in the outputs of the deployment. To find that, click on the link under <code>Last deployment</code> (which is <code>4/15/2016 (Succeeded)</code> here) and you should see this:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step2a.png" alt="Deployment history" /></p>

<p>Click on the latest deployment and copy the value of <code>MASTERFQDN</code> in the <code>Outputs</code> section:</p>

<p><img src="/assets/images/dcos-azure-marketplace-step2b.png" alt="Deployment output" /></p>

<p>Use the value of <code>MASTERFQDN</code> you found in the <code>Outputs</code> section in the previous step and paste it in the following command:</p>

<p>$ ssh azureuser@masterfqdn -p 2200 -L 8000:localhost:80</p>

<p>For example, in my case:</p>

<p>$ ssh azureuser@dcosmaster.westus.cloudapp.azure.com -p 2200 -L 8000:localhost:80</p>

<p>Now you can visit <code>http://localhost:8000</code> on your local machine and view the DC/OS Dashboard.</p>

<p><img src="/assets/images/ui-dashboard.gif" alt="DC/OS dashboard" /></p>

<h3>Caveats</h3>

<p>Some caveats around SSH access:</p>

<ul>
<li>For connections to <code>http://localhost:8000</code> to work, the SSH command must be run on your local machine, and not inside a Virtual Machine.</li>
<li>In the example above, port <code>8000</code> is assumed to be available on your local machine.</li>
<li>The SSH commands shown only work on Mac or Linux. For Windows use <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">Putty</a> with a similar port-forwarding configuration, see also <a href="https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-ssh-from-windows/">How to Use SSH with Windows on Azure</a>.</li>
<li>If you want to learn more about SSH key generation check out this <a href="https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/">GitHub tutorial</a>.</li>
</ul>

<p>The DC/OS UI will not show the correct IP address or CLI install commands when connected by using an SSH tunnel.</p>

<p>Note that the following commands can be used to run the DC/OS CLI directly on the master node:</p>

<p># Connect to master node with ssh
   $ ssh -p2200 azureuser@MASTER_LOAD_BALANCER -L 8000:localhost:80</p>

<p># Install virtualenv
   $ sudo apt-get -y install virtualenv</p>

<p># Install CLI on the master node and configure with http://localhost
   $ mkdir -p dcos &amp;&amp; cd dcos &amp;&amp;
   $ curl -O https://downloads.dcos.io/dcos-cli/install-optout.sh &amp;&amp; 
      bash ./install-optout.sh . http://localhost &amp;&amp; 
      source ./bin/env-setup</p>

<p># Now you can use the DC/OS CLI:
   $ dcos package search</p>

<h2>Tear Down the DC/OS cluster</h2>

<p>If you've created a new resource group in the deployment step, it is as easy as this to tear down the cluster and release all of the resources: just delete the resource group. If you have deployed the cluster into an existing resource group, you'll need to identify all resources that belong to the DC/OS cluster and manually delete them.</p>

<h2>Next steps</h2>

<ul>
<li><a href="/administration/user-management/">Add users to your cluster</a></li>
<li><a href="/usage/cli/install/">Install the DC/OS Command-Line Interface (CLI)</a></li>
<li><a href="/usage/">Use your cluster</a></li>
<li><a href="https://azure.microsoft.com/en-us/documentation/articles/best-practices-auto-scaling/">Scaling considerations</a></li>
</ul>
