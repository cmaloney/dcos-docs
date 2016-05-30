---
layout: page
post_title: Marathon
menu_order: 0
---



Mesosphere Enterprise DC/OS uses Marathon to manage the processes and services and is the "init system" for the DC/OS. Marathon starts and monitors your applications and services, automatically healing failures.

A native Marathon instance is installed as a part of DC/OS installation. After DC/OS has started, you can manage the native Marathon instance through the web interface at `<hostname>/marathon` or from the DC/OS CLI with the `dcos marathon` command.

You can also create additional Marathon instances for specific users. 

# Install a Single Additional Marathon instance

**Prerequisite**

*   The DC/OS CLI must be [installed][1].

1. Enter this command from the DC/OS CLI:
        
            $ dcos package install marathon

By default the DC/OS service name is `marathon-user`. <a href="/wp-content/uploads/2015/12/marathontask.png" rel="attachment wp-att-1410"><img src="/wp-content/uploads/2015/12/marathontask.png" alt="marathontask" width="709" height="44" class="alignnone size-full wp-image-1410" /></a>
                
1. In the DC/OS web interface, click the **System** tab, select **Organization**, and then **Groups**.

1. Click the `Superuser group`, then **Advanced ACLs**. [More information about advanced ACLs](https://docs.mesosphere.com/administration/security-and-authentication/advanced-acl/).

1. Add the following two rules:

<table class="table">
  <tr>
    <th>Resource</th>
    <th>Permissions</th>
  </tr>
  <tr>
    <td>dcos:service:marathon:marathon-user:services/</td>
    <td>full</td>
  </tr>
  <tr>
  	 <td>dcos:service:marathon:marathon-user:admin/</td>
  	 <td>full</td>
  </tr>
</table>

Then, click **Add Rule**.

1. Close the `Superuser group` panel. Click the **Services** tab, then choose your Marathon service name to navigate to the web interface. For more information, see [Deploying Multiple Marathon Instances][2].
        
# Install Multiple Additional Marathon Instances

**Prerequisite**

*   The DC/OS CLI must be [installed][1].      

1.  Create a custom JSON configuration file that includes this entry where `<name>` is the unique Marathon instance name:
            
                 {"service": {"name": "marathon-<name>" }}
                
**Note:** You must create a separate JSON configuration file for each Marathon instance.
        
1. From the DC/OS CLI, enter this command:
            
                 $ dcos package install --options=<config-file>.json marathon

1. In the DC/OS web interface, open the ACL editor add the rules for group `superusers` for each of your new Marathon instances. [More information about advanced ACLs](https://docs.mesosphere.com/administration/security-and-authentication/advanced-acl/).

<table class="table">
  <tr>
    <th>Resource</th>
    <th>Permissions</th>
  </tr>
  <tr>
    <td>dcos:service:marathon:<service-name>:services/</td>
    <td>full</td>
  </tr>
  <tr>
  	 <td>dcos:service:marathon:<service-name>:admin/</td>
  	 <td>full</td>
  </tr>
</table>

Then, click **Add Rule**.

1. Click the **Services** tab, the choose one of your Marathon service names in order to navigate to the web interface. For more information, see [Deploying Multiple Marathon Instances][2].

# Uninstalling a Marathon Instance

1.  From the DC/OS CLI, enter this command:

        $ dcos package uninstall --app-id=<name> marathon
        

2.  Open the Zookeeper Exhibitor web interface at `<hostname>/exhibitor`, where `<hostname>` is the [Mesos Master hostname][3].
    
    1.  Click on the **Explorer** tab and navigate to the `universe/<service-name>` folder.
        
        **Important:** Do not delete the `marathon` folder. This is the native DC/OS Marathon instance.
        
        <a href="/wp-content/uploads/2015/12/zkmarathon.png" rel="attachment wp-att-1407"><img src="/wp-content/uploads/2015/12/zkmarathon-600x482.png" alt="zkmarathon" width="300" height="241" class="alignnone size-medium wp-image-1407" /></a>
    
    2.  Choose Type **Delete**, enter the required **Username**, **Ticket/Code**, and **Reason** fields, and click **Next**.
        
        <a href="/wp-content/uploads/2015/12/zkmarathondelete.png" rel="attachment wp-att-1409"><img src="/wp-content/uploads/2015/12/zkmarathondelete-600x331.png" alt="zkmarathondelete" width="300" height="166" class="alignnone size-medium wp-image-1409" /></a>
    
    3.  Click **OK** to confirm your deletion.

For more information:

*   <a href="http://mesosphere.github.io/marathon/docs/" target="_blank">Marathon documentation</a>
*   [Deploying a Web App][4]

 [1]: /usage/cli/install/
 [2]: /usage/services/marathon/marathon-user-instance/
 [3]: /administration/installing/cloud/aws#launchdcos
 [4]: /tutorials/containerized-app/
