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
You can create a DC/OS cluster for Amazon Web Services (AWS) by using the DC/OS template on AWS CloudFormation.

Depending on the DC/OS services that you install, you might have to modify the DC/OS templates to suit your needs. For more information, see [Scaling the DC/OS cluster in AWS][1].

# System requirements

## Hardware

An Amazon EC2 <a href="https://aws.amazon.com/ec2/pricing/" target="_blank">m3.xlarge</a> instance.  Selecting smaller-sized VMs is not recommended, and selecting fewer VMs will likely cause certain resource-intensive services, such as distributed datastores, to not work properly.

*   You have the option of 1 or 3 Mesos master nodes.
*   5 [private](/overview/concepts/#private) Mesos agent nodes is the default. 
*   1 [public](/overview/concepts/#public) Mesos agent node is the default. 

## Software

- An AWS account.
- SSH installed and configured. This is required for accessing nodes in the DC/OS cluster.

# Install DC/OS

**Prerequisite:**
You must have an Amazon EC2 Key Pair for the same region as your cluster. Key pairs cannot be shared across regions. The AWS key pair uses public-key cryptography to provide secure login to your AWS cluster. For more information about creating an Amazon EC2 Key Pair, see the <a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair" target="_blank">documentation</a>. 

1.  Launch the <a href="http://mesosphere.com/amazon/setup" target="_blank">DC/OS template</a> on CloudFormation and select the region and number of masters.
    
2.  On the **Select Template** page, accept the template specified for you and click **Next**.

3.  On the **Specify Details** page, specify a cluster name (`Stack name`), Key Pair (`KeyName`), authentication (`OAuthEnabled`), public agent (`PublicSlaveInstanceCount`), private agent (`SlaveInstanceCount`), and click **Next**. 

    **Important:** The DC/OS template is configured for running DC/OS. If you modify the template you might be unable to run certain packages on your DC/OS cluster. 
    
    ![AWS specify details](/assets/images/awstemplateparms.png)

4.  On the **Options** page, accept the defaults and click **Next**.
    
    **Tip:** You can choose whether to rollback on failure. By default this option is set to **Yes**.

5.  On the **Review** page, check the acknowledgement box and then click **Create**.
    
    **Tip:** If the **Create New Stack** page is shown, either AWS is still processing your request or you’re looking at a different region. Navigate to the correct region and refresh the page to see your stack.

# Monitor the DC/OS cluster convergence process

In <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">CloudFormation</a> you should see:

*   The cluster stack spins up over a period of 10 to 15 minutes.

*   The status changes from CREATE_IN_PROGRESS to CREATE_COMPLETE.

**Troubleshooting:** A ROLLBACK_COMPLETE status means the deployment has failed. See the **Events** tab for useful information about failures. For more information, see the <a href="https://support.mesosphere.com/hc/en-us/articles/205316535-Why-did-my-AWS-cluster-Rollback-" target="_blank">Mesosphere Knowledge Base</a>.

# <a name="launchdcos"></a>Launch DC/OS

Launch the DC/OS web interface by entering the Mesos Master hostname:

1.  From the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page, click to check the box next to your stack.

2.  Click on the **Outputs** tab and copy/paste the Mesos Master hostname into your browser to open the DC/OS web interface. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname.
    
    **Tip:** You might need to resize your window to see this tab. You can find your DC/OS hostname any time from the <a href="https://console.aws.amazon.com/cloudformation/home" target="_blank">Amazon CloudFormation Management</a> page.
    
    <a href="/wp-content/uploads/2015/12/awsscreenshot2.png" rel="attachment wp-att-1167"><img src="/wp-content/uploads/2015/12/awsscreenshot2.png" alt="awsscreenshot2" width="621" height="146" class="alignnone size-full wp-image-1167" /></a>

3.  [Install the DC/OS Command-Line Interface (CLI)][5]. You must install the CLI to administer your DC/OS cluster.
    
    You also have the option to take a brief tutorial that walks you through the basics of using the Mesosphere DC/OS. You can restart this tutorial anytime by clicking the signpost icon in the lower left corner.
    
    <a href="/wp-content/uploads/2015/12/dashboardsmall.png" rel="attachment wp-att-1120"><img src="/wp-content/uploads/2015/12/dashboardsmall-800x495.png" alt="dashboardsmall" width="800" height="495" class="alignnone size-large wp-image-1120" /></a>

# Next steps

*   Try out the [Deploying a Containerized App on a Public Node][6] tutorial.

 [1]: /administration/managing-a-dcos-cluster-in-aws/#scaling
 [2]: /overview/security/#scrollNav-1
 [3]: /overview/security/#scrollNav-2
 [4]: /overview/security/#scrollNav-3
 [5]: /usage/cli/install/
 [6]: /usage/tutorials/containerized-app/