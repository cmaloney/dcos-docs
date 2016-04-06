---
UID: 56fb10d937a6a
post_title: Creating ACL Group Definitions
post_excerpt: ""
layout: page
published: true
menu_order: 0
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: true
hide_from_related: true
---
You can define fine-grained access to applications that are running in DCOS by defining advanced ACL groups. Advanced ACL groups can provide multi-tenancy by isolating application teams, and individual users. You can also control customized access to applications, for example read-only access. This access is administered on the backend by Admin Router and the native Marathon instance.

Authorization is not secure, but provides user isolation. Anything running inside the cluster, and anyone SSH’d into it, has access to everything.

By default, when you install a DCOS service, an entry is created in the ACL for that particular service. For example, when you install Kafka, this ACL entry is created:

    dcos:adminrouter:service:kafka
    

You can create advanced ACL groups for each role in your organization. 

To create an advanced ACL group:

1.  Launch the DCOS web interface and login with your Admin username and password.

2.  Click on the **System** tab, select **Organization**, and then **Groups**.

3.  Click **New Group** button and provide a name for your group.

4.  Open the new group and click **Advanced ACLs**.

5.  Define your advanced ACL by providing Resource and Permission and then click **Add Rule**.

    ![alt text](/assets/auth-enable-advanced-acl.gif)
    
    ### Resource 
    Here are the available resource options.
    
    *   `dcos:service` - resources defined by a DCOS service such as Cassandra. 
        <p>
        <table>
          <tr>
            <th>Resource</th>
            <th>Description</th>
            <th>Permission</th>
          </tr>
          <tr>
            <td>dcos:service:&lt;service-name&gt;</td>
            <td>Controls access to resources for an installed DCOS Service from the package repository.</td>
            <td>create, read, update, delete</td>
          </tr>
          <tr>
          </tr>
        </table>
        </p>
    *   `dcos:adminrouter` - resources defined by Admin Router, such as locations.
        <p>
        <table>
          <tr>
            <th>Resource</th>
            <th>Description</th>
            <th>Permission</th>
          </tr>
          <tr>
            <td>`dcos:adminrouter:acs`</td>
            <td>Controls access to the access control service API through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:package`</td>
            <td>Controls access to the packaging API through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:mesos`</td>
            <td>Controls access to Mesos through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:exhibitor`</td>
            <td>Controls access to Exhibitor through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:slave`</td>
            <td>Controls access to Admin Router's /slave endpoint</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:metadata`</td>
            <td>Controls access to Admin Router's medatada endpoints</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:historyservice`</td>
            <td>Controls access to the history service through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:mesos-dns`</td>
            <td>Controls access to Mesos DNS through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:networking`</td>
            <td>Controls access to the networking API through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:ops:system-health`</td>
            <td>Controls access to the health monitoring API through Admin Router</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:adminrouter:service:marathon`</td>
            <td>Controls access to the DCOS Marathon instance. For example, you can grant a user access to `http://<public-master-IP>/service/marathon`.</td>
            <td>full</td>
          </tr>
          <tr>
          </tr>
        </table>
        </p>
    <!-- For 1.9 *   `dcos:acs` - resources defined by the access control service. -->
    *   `dcos:service:marathon`
        <p>
        <table>
          <tr>
            <th>Resource</th>
            <th>Description</th>
            <th>Permission</th>
          </tr>
          <tr>
            <td>`dcos:service:marathon:services/`</td>
            <td>Controls access to all apps managed by DCOS Marathon.</td>
            <td>full</td>
          </tr>
          <tr>
            <td>`dcos:service:marathon:marathon:services/production`</td>
            <td>Controls group access to the DCOS Marathon instance.</td>
            <td>create, read, update, delete</td>
          </tr>
          <tr>
            <td>`dcos:service:marathon:<user-marathon>:services/production`</td>
            <td>Controls group access to an installed Marathon instance (`<user-marathon>`).</td>
            <td>create, read, update, delete</td>
          </tr>
        </table>
        </p>
    *   `dcos:superuser` - superusers are allowed to do everything.
        <p>
        <table>
          <tr>
            <th>Resource</th>
            <th>Description</th>
            <th>Permission</th>
          </tr>
          <tr>
            <td>dcos:superuser</td>
            <td>Superuser group</td>
            <td>full</td>
          </tr>
        </table>
        </p>
            
    



# Examples

In this example, read-only access to Chronos is granted to an existing user named Ted:

1.  Click on the **System** and then the **Organization** tab. 

1.  Click **Groups** and create a new group named `Chronos Users`.

    ![alt text](/assets/auth-enable-chronos-example.gif)
    
1.  Open the Chronos Users group and click **Advanced ACLs**. 

1.  Create an ACL group with Resource `dcos:adminrouter:chronos:services` and Permission `read`.

    ![alt text](/assets/auth-enable-chronos-example2.gif)
    
1.  Click **Members** -> **Add User** and add Ted to this group. 

    ![alt text](/assets/auth-enable-chronos-example3.gif)
    
1.  Click **Close** to save these changes.
    
1.  Go to the Services tab and copy the URL for the Chronos service, for example `http:<public-master-IP>/#/services/ui/chronos`, and send to Ted. Ted can click this link to access the Chronos. 

    ![alt text](/assets/auth-enable-chronos-example4.gif)



 [1]: https://www.ietf.org/rfc/rfc3986.txt