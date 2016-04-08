---
UID: 56f9844a87b97
post_title: Installing on Amazon Web Services
post_excerpt: ""
layout: page
published: true
menu_order: 1
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
You can create a DCOS cluster for Amazon Web Services (AWS) by using the Mesosphere DCOS template on AWS CloudFormation.

**Important:** The DCOS Community Edition (CE) is available at no cost, but must be run on a supported cloud provider (for example, Amazon Web Services). While there are no licensing fees for DCOS CE, your cloud provider charges for their service. For an estimate of the cost of running DCOS CE on AWS, see <a href="https://support.mesosphere.com/hc/en-us/articles/205314895-How-much-does-running-a-default-DCOS-cluster-configuration-cost-" target="_blank">this FAQ entry</a>.

The DCOS cloud templates are optimized to run Mesosphere DCOS. Depending on the DCOS services that you install, you might have to modify the DCOS templates to suit your needs. You can modify the cloud templates, but Mesosphere cannot assist in troubleshooting. If you require support, please consider the DCOS Enterprise Edition. For more information, see [Scaling the DCOS cluster in AWS][1].

### AWS CloudFormation template

*   1 or 3 Mesos master nodes in the [admin][2] zone
*   5 Mesos agent nodes in the [private][3] zone
*   1 Mesos agent node in the [public][4] zone
*   Amazon EC2 <a href="https://aws.amazon.com/ec2/pricing/" target="_blank">m3.xlarge</a> instance

# Create a DCOS cluster

**Prerequisite:**

<a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair" target="_blank">Amazon EC2 Key Pair</a>

1.  Launch the <a href="http://mesosphere.com/amazon/setup" target="_blank">DCOS template</a> on CloudFormation and select the region and number of masters. You must have a key pair for your selected region.
    
    <!-- a href="http://mesosphere.com/amazon/setup" target="_blank">DCOS template</a>: The current stable release. -->
    
    <!-- - <a href="https://downloads.mesosphere.com/dcos/EarlyAccess/aws.html">DCOS 1.3 early access template</a> -->
    
    **Important:** The Mesosphere template is configured for running DCOS. If you modify the template you might be unable to run certain packages on your DCOS cluster. For more information, see the <a href="https://support.mesosphere.com/hc/en-us/articles/205674655-How-can-I-modify-the-DCOS-template-on-AWS-CloudFormation-" target="_blank">Knowledge Base</a>.

2.  On the **Select Template** page, accept the template specified for you and click **Next**.

3.  On the **Specify Details** page, specify a cluster name, SSH key, accept the <a href="/community-edition-eula/" target="_blank">EULA</a>, and click **Next**. The other parameters are optional.
    
    <a href="/wp-content/uploads/2015/12/awstemplateparms.png" rel="attachment wp-att-1218"><img src="/wp-content/uploads/2015/12/awstemplateparms.png" alt="awstemplateparms" width="551" height="326" class="alignnone size-full wp-image-1218" /></a>

4.  On the **Options** page, accept the defaults and click **Next**.
    
    **Tip:** You can choose whether to rollback on failure. By default this option is set to **Yes**.

5.  On the **Review** page, check the acknowledgement box and then click **Create**.
    
    **Tip:** If the **Create New Stack** page is shown, either AWS is still processing your request or you’re looking at a different region. Navigate to the correct region and refresh the page to see your stack.

# Monitor the DCOS cluster convergence process

In <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">CloudFormation</a> you should see:

*   The cluster stack spins up over a period of 10 to 15 minutes.

*   The status changes from CREATE_IN_PROGRESS to CREATE_COMPLETE.

**Troubleshooting:** A ROLLBACK_COMPLETE status means the deployment has failed. See the **Events** tab for useful information about failures. For more information, see the <a href="https://support.mesosphere.com/hc/en-us/articles/205316535-Why-did-my-AWS-cluster-Rollback-" target="_blank">Mesosphere Knowledge Base</a>.

# <a name="launchdcos"></a>Launch DCOS

Launch the DCOS web interface by entering the Mesos Master hostname:

1.  From the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page, click to check the box next to your stack.

2.  Click on the **Outputs** tab and copy/paste the Mesos Master hostname into your browser to open the DCOS web interface. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname.
    
    **Tip:** You might need to resize your window to see this tab. You can find your DCOS hostname any time from the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page.
    
    <a href="/wp-content/uploads/2015/12/awsscreenshot2.png" rel="attachment wp-att-1167"><img src="/wp-content/uploads/2015/12/awsscreenshot2.png" alt="awsscreenshot2" width="621" height="146" class="alignnone size-full wp-image-1167" /></a>

3.  [Install the DCOS Command-Line Interface (CLI)][5]. You must install the CLI to administer your DCOS cluster.
    
    You also have the option to take a brief tutorial that walks you through the basics of using the Mesosphere DCOS. You can restart this tutorial anytime by clicking the signpost icon in the lower left corner.
    
    <a href="/wp-content/uploads/2015/12/dashboardsmall.png" rel="attachment wp-att-1120"><img src="/wp-content/uploads/2015/12/dashboardsmall-800x495.png" alt="dashboardsmall" width="800" height="495" class="alignnone size-large wp-image-1120" /></a>

# Next steps

*   Try out the [Deploying a Containerized App on a Public Node][6] tutorial.

 [1]: /administration/managing-a-dcos-cluster-in-aws/#scrollNav-1
 [2]: /overview/security/#scrollNav-1
 [3]: /overview/security/#scrollNav-2
 [4]: /overview/security/#scrollNav-3
 [5]: /usage/cli/install/
 [6]: /usage/tutorials/containerized-app/