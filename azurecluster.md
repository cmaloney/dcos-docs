---
post_title: 'Private: Creating a DCOS Cluster For Azure (Early Access)'
menu_order: 51
---
You can create a DCOS cluster for Azure by using the Mesosphere [DCOS template][1].

**Important:**

*   A Microsoft Azure organization account is required for DCOS early access. We recommend that you have a minimum of 20 cores available in your Azure subscription to support DCOS. For more information about creating an organization ID, see <a href="https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-work-id-from-personal/" target="_blank">Creating a Work or School identity in Azure Active Directory</a>.
*   Mesosphere operates independently of Microsoft Azure to run DCOS Community Edition (CE). While there are no licensing fees associated with the use of DCOS CE, standard resource usage fees are assessed by Microsoft Azure.

### <a name="create"></a>Creating a DCOS cluster by using Azure

1.  Go to the [Azure Portal][2].

2.  Create a new DCOS cluster in the Azure Portal by copying and pasting the DCOS Azure template URL into your browser:
    
        https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdownloads.mesosphere.com%2Fdcos%2FEarlyAccess%2Fazuredeploy.json
        
    
    **Important:** There is a known bug, if you do not see the template at this point, please cut and paste the link again. Mesosphere and Microsoft are working on a fix.

3.  Click the **TEMPLATE** tab, then click **Save** on the bottom of the page to accept this template. **Tip:** If you don't click **Save** you cannot move on to the next steps.

4.  Click the **PARAMETERS** tab and customize your deployment:
    
    1.  SSHKEYDATA: enter your public SSH key, which you can use to ssh into the master node.
    2.  AUTHORIZEDSUBNET: Enter “Internet” for full access, or an IP4 subnet to restrict access to this cluster to a specific set of IP addresses.
    3.  REGION: Select the Azure region where you want this cluster to be provisioned.
    4.  Click the **OK** to save your parameters.

5.  Click the **SUBSCRIPTION** tab and select a subscription.

6.  Click the **RESOURCE GROUP** tab and select an existing resource group name or create a new name.
    
    **Important:** The DCOS resource group name must be unique within Microsoft Azure, contain only alphanumeric characters, and have a length between 3 and 19 characters. This name is the DNS prefix name and can be used to access blobs, queues, and tables in the storage account.

7.  Click the **RESOURCE GROUP LOCATION** and select a resource group location.

8.  Click **Create** to create your new cluster. This will take a few minutes.

9.  When your cluster is ready, a new resource group icon on the right side of the portal landing page appears:
    
    1.  Click on your resource group icon. You can also find your resource group by navigating to **Browse Everything -> Resource Groups**.
    2.  From the resource group, select the **Last deployment** status under **Essentials**. As your new cluster is created the status will change from Running to Succeeded.
    3.  Click on the **Running** deployment status, then on **Microsoft.Template** to see a summary of the deployment.
    
    **Tip:** To SSH into the master node of a new DCOS cluster on Azure, enter this command:
    
        ssh -A core@<mesos master hostname>
        

10. You can now access the Mesosphere DCOS dashboard.

11. Copy and save your DCOS master cluster hostname from the **masterurl0** field. This hostname is used to access the DCOS Dashboard web interface, Marathon web interface, and for installing the DCOS CLI.

12. Enter the master hostname into your browser to open the DCOS GUI. The interface runs on the standard HTTP port 80, so you do not need to specify a port number after the hostname. The first time you open the GUI, you’ll be prompted for your email address. This email is your identity in the Mesosphere Intercom feedback system. <img src="{% asset_path dashboardlarge.png %}" alt="" width="100%" />

 [1]: /getting-started/dcostemplate/
 [2]: https://portal.azure.com