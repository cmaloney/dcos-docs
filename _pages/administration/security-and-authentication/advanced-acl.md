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
You can define fine-grained access to applications that are running in DC/OS by defining advanced ACL groups. Advanced ACL groups can provide multi-tenancy by isolating application teams, and individual users. You can also control customized access to applications, for example read-only access. This access is administered on the backend by Admin Router and the native Marathon instance.

Authorization is not secure, but provides user isolation. Anything running inside the cluster, and anyone SSH’d into it, has access to everything.
    

You can create advanced ACL groups for each role in your organization. 

To create an advanced ACL group:

1.  Launch the DC/OS web interface and login with your Admin username and password.

2.  Click on the **System** tab, select **Organization**, and then **Groups**.

3.  Click **New Group** button and provide a name for your group.

4.  Open the new group and click **Advanced ACLs**.

5.  Define your advanced ACL by providing Resource and Permission and then click **Add Rule**.

    ![alt text](/assets/images/auth-enable-advanced-acl.gif)
    
    Here are the available resource options.
    
    ### DC/OS core components
    Access to these resources is controlled by the Admin Router.
    
    <p>
    <table class="table">
      <tr>
        <th>Resource</th>
        <th>Permission</th>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:package</code><p>This resource controls access to the DC/OS packaging API. Users with access can install DC/OS services from the DC/OS package repository.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:ops:mesos</code><p>This resource controls access to Mesos UI. Users with access can navigate to the Mesos UI.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:ops:exhibitor</code><p>This resource controls access to Exhibitor UI.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:ops:slave</code><p>This resource controls access to Admin Router's agent endpoint.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:service:marathon</code><p>This resource controls access to the DC/OS Marathon instance. For example, you can grant a user access to `http://<public-master-IP>/service/marathon`.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:adminrouter:service:&lt;service-name&gt;</code><p>This resource controls access to installed DC/OS Services.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
      <td><code>dcos:superuser</code><p>This resource controls access to the the superuser group. This group has access to all DC/OS components and is where DC/OS administrator access is defined.</p></td>
      <td><code>full</code></td>
    </tr>
      <tr>
      </tr>
    </table>
    </p>
    <!-- For 1.9 *   `dcos:acs` - resources defined by the access control service. -->

    ### DC/OS Marathon 
    Access to these resources is controlled by the DC/OS Marathon instance.
    
    Marathon Resource Identifiers follow this schema: <code>dcos:service:marathon:{service name}:services/{group id}</code>
    
    Restrictions apply to applications and application groups in one service. 
    Valid actions for a path are: `create`, `read`, `update`, `delete`. A shortcut for all actions is `full`.
    
    Since you can have multiple Marathons installed in your cluster, you can define rules for every service separately.
    The `service name` must be RFC 3986 (URI) compliant. 
    The DC/OS ACS is responsible for enforcing this. 
    The service name is passed to Marathon via the  `--framework_name` command line argument. 
    
    The `group id` is the ID of the group in Marathon.
    Applications and Application Groups in Marathon build a tree. 
    Each can be identified via their path from the root group, e.g. `/production/frontend/webserver`. 
    An ACL rule will grant access to the entire subtree rooted at the path represented by the resource identifier. For example, if a user is granted create permission on `/production`, then they are allowed to create an app named `/production/frontend/webserver`.


    Examples:
    <p>
    <table class="table">
      <tr>
        <th>Resource</th>
        <th>Permission</th>
      </tr>
      <tr>
        <td><code>dcos:service:marathon:marathon</code><p>This resource controls access to all apps managed by DC/OS Marathon.</p></td>
        <td><code>full</code></td>
      </tr>         
      <tr>
        <td><code>dcos:service:marathon:marathon-user</code><p>This resource controls access to all apps managed by the installed Marathon service <code>marathon-user</code>.</p></td>
        <td><code>full</code></td>
      </tr>         
      <tr>
        <td><code>dcos:service:marathon:marathon:services/service-group</code><p>This resource restricts group access to the group <code>service-group</code> in DC/OS Marathon instance.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:service:marathon:marathon-user:services/service-group</code><p>This resource controls group access to an installed Marathon instance <code>marathon-user</code>.</p></td>
        <td><code>full</code></td>
      </tr>
      <tr>
        <td><code>dcos:service:marathon:marathon:services/group1/group2</code><p>This resource restricts group access to the group <code>/group1/group2</code> in DC/OS Marathon instance.</p></td>
        <td><code>create</code>, <code>read</code>,<code>update</code>, <code>delete</code></td>
      </tr>
    </table>
    </p>
    
    After creating a service level rule, make sure the related group exists in the Marathon instance.
    Users with limited access rights will only see applications and groups according to the defined ACLs.


# Examples 

## Grant user admin access to DC/OS
In this example, superuser access is granted to an existing user named Carlos.

1.  Click on the **System** and then the **Organization** tab. 

1.  Click **Groups** and select **Superuser group**.

1.  Click **Members** **Add User** and add Carlos to this group.

    ![add user carlos](/assets/images/auth-enable-ex-superuser.gif)
    
1.  Click **Close** to save these changes.

1.  Send Carlos a link to the web interface.

<!-- ## Grant user access to a Marathon app group -->


## Grant user access to install packages
In this example, a user named Ted is granted access to install services from the DC/OS package repository.

1.  Click on the **System** and then the **Organization** tab. 

1.  From the **Groups** tab click **New Group**. 

1.  Enter `DC/OS package` for Group name and click **Create**.

    ![alt text](/assets/images/auth-enable-ex-package-user.gif)
    
1.  Open the DC/OS package group. 

    1.  From the **Advanced ACLs** tab, enter Resource `dcos:adminrouter:package`, Permission `full`, and click **Add Rule**.

        ![alt text](/assets/images/auth-enable-ex-package-user2.gif)
            
    1.  From the **Members** tab, select Ted from the dropdown menu.
    
    1.  Click **Close** to save the changes.
    
1.  Log in to the CLI as Ted.

        $ dcos package install kafka
        52.26.144.2's username: ted
        ted@52.26.144.2's password: 
        [core.dcos_acs_token]: set to 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiJ0ZWQiLCJleHAiOiIxNDYwNDE1NjY0In0.IGk3OOPCpoEUTnaY-V4OMJ1DOGl8YWau8Hd4uedc5lI'
        This will install Apache Kafka DC/OS Service.
        Continue installing? [yes/no]


 [1]: https://www.ietf.org/rfc/rfc3986.txt
