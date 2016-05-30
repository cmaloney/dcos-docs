---
layout: page
post_title: AWS DC/OS Installation Guide
menu_order: 0
---
<p>You can create a DC/OS cluster for Amazon Web Services (AWS) by using the DC/OS template on AWS CloudFormation.</p>

<p>Depending on the DC/OS services that you install, you might have to modify the DC/OS templates to suit your needs. For more information, see <a href="/administration/managing-aws/">Scaling the DC/OS cluster in AWS</a>.</p>

<h1>System requirements</h1>

<h2>Hardware</h2>

<p>An Amazon EC2 <a href="https://aws.amazon.com/ec2/pricing/" target="_blank">m3.xlarge</a> instance.  Selecting smaller-sized VMs is not recommended, and selecting fewer VMs will likely cause certain resource-intensive services, such as distributed datastores, to not work properly.</p>

<ul>
<li>You have the option of 1 or 3 Mesos master nodes.</li>
<li>5 <a href="/overview/concepts/#private">private</a> Mesos agent nodes is the default.</li>
<li>1 <a href="/overview/concepts/#public">public</a> Mesos agent node is the default.</li>
</ul>

<h2>Software</h2>

<ul>
<li>An AWS account.</li>
<li>SSH installed and configured. This is required for accessing nodes in the DC/OS cluster.</li>
</ul>

<h1>Install DC/OS</h1>

<p><strong>Prerequisite:</strong>
You must have an Amazon EC2 Key Pair for the same region as your cluster. Key pairs cannot be shared across regions. The AWS key pair uses public-key cryptography to provide secure login to your AWS cluster. For more information about creating an Amazon EC2 Key Pair, see the <a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair" target="_blank">documentation</a>.</p>

<ol>
<li><p>Launch the <a href="https://downloads.dcos.io/dcos/EarlyAccess/aws.html" target="_blank">DC/OS template</a> on CloudFormation and select the region and number of masters (1 or 3). You must have a key pair for your selected region.</p></li>
<li><p>On the <strong>Select Template</strong> page, accept the defaults and click <strong>Next</strong>.</p>

<p><img src="/assets/images/dcos-aws-step2b.png" alt="Launch stack" /></p></li>
<li><p>On the <strong>Specify Details</strong> page, specify a cluster name (<code>Stack name</code>), Key Pair (<code>KeyName</code>), authentication (<code>OAuthEnabled</code>), public agent (<code>PublicSlaveInstanceCount</code>), private agent (<code>SlaveInstanceCount</code>), and click <strong>Next</strong>.</p>

<p><strong>Important:</strong> The DC/OS template is configured for running DC/OS. If you modify the template you might be unable to run certain packages on your DC/OS cluster.</p>

<p><img src="/assets/images/dcos-aws-step2c.png" alt="Create stack" /></p></li>
<li><p>On the <strong>Options</strong> page, accept the defaults and click <strong>Next</strong>.</p>

<p><strong>Tip:</strong> You can choose whether to rollback on failure. By default this option is set to <strong>Yes</strong>.</p></li>
<li><p>On the <strong>Review</strong> page, check the acknowledgement box and then click <strong>Create</strong>.</p>

<p><strong>Tip:</strong> If the <strong>Create New Stack</strong> page is shown, either AWS is still processing your request or you’re looking at a different region. Navigate to the correct region and refresh the page to see your stack.</p></li>
</ol>

<h1>Monitor the DC/OS cluster convergence process</h1>

<p>In <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">CloudFormation</a> you should see:</p>

<ul>
<li><p>The cluster stack spins up over a period of 10 to 15 minutes.</p></li>
<li><p>The status changes from CREATE_IN_PROGRESS to CREATE_COMPLETE.</p></li>
</ul>

<p><strong>Troubleshooting:</strong> A ROLLBACK_COMPLETE status means the deployment has failed. See the <strong>Events</strong> tab for useful information about failures.</p>

<h1><a name="launchdcos"></a>Launch DC/OS</h1>

<p>Launch the DC/OS web interface by entering the Mesos Master hostname:</p>

<ol>
<li><p>From the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page, click to check the box next to your stack.</p></li>
<li><p>Click on the <strong>Outputs</strong> tab and copy/paste the Mesos Master hostname into your browser to open the DC/OS web interface. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname.</p>

<p><strong>Tip:</strong> You might need to resize your window to see this tab. You can find your DC/OS hostname any time from the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page.</p>

<p><img src="/assets/images/dcos-aws-step3a.png" alt="Monitor stack creation" /></p>

<p><img src="/assets/images/ui-dashboard.gif" alt="DC/OS dashboard" /></p></li>
<li><p>Click the dropup menu on the lower-left side to install the DC/OS <a href="/usage/cli/install/">Command-Line Interface (CLI)</a>. You must install the CLI to administer your DCOS cluster.</p>

<p><img src="/assets/images/ui-dashboard-install-cli.gif" alt="install CLI" /></p></li>
</ol>

<h1>Next steps</h1>

<ul>
<li><a href="/administration/user-management/">Add users to your cluster</a></li>
<li><a href="/usage/">Using your cluster</a></li>
<li><a href="https://aws.amazon.com/autoscaling/">Scaling considerations</a></li>
</ul>
