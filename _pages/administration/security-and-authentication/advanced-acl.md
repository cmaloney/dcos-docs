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
    A resource has this format: 
    
        dcos:<service-type>:<service-name>:<namespace>/<object-id>
    
    *   **dcos**: The required prefix.
    *   **service-type**: The service type: 
        *   `dcos:service` - resources defined by a DCOS service such as Cassandra. 
        *   `dcos:adminrouter` - resources defined by Admin Router, such as locations.
        <!-- For 1.9 *   `dcos:acs` - resources defined by the access control service. -->
        *   `dcos:superuser` - superusers are allowed to do everything.
    *   **service-name** An [RFC 3986 (URI)][1] compliant name. For example, `dcos:service:kafka`. 
    *   **namespace** The namespace can be: 
        *   `dcos:<service-type>:<service-name>:services` - Marathon ACLs. 
        *   `dcos:<service-type>:<service-name>:admin` - Admin Router ACLs.
    *   **<object-id>** 
    
    ### Permission
    The type of permissions assigned to the resource. The available permissions are determined by the type of namespace, `services` or `admin`.
    
    The available `dcos:<service-type>:<service-name>:services` namespace permissions are:
    
    *   `create` - Create an application. 
    *   `delete` - Delete an application.
    *   `read` - Read-only access to an application. 
    *   `update` - Update an application.
    
    The available `admin` namespace permission is:
    
    *   `full` - Full CRUD access. This is the only available option for the `dcos:<service-type>:<service-name>:admin` namespace. 
    
    
Available resources:



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


In this example, a group is created that has access to all Marathon services in DCOS, where `<service-type>` is `marathon`, `<service-name>` is `marathon`, `<namespace>` is `services`, and `<object-id>` is `/`.

    dcos:service:marathon:marathon:services/

In this example, a group is created that has access to the `user-marathon` instance `production` subgroup, where `<service-type>` is `marathon`, `<service-name>` is `user-marathon`, `<namespace>` is `services`, and `<object-id>` is `/production`.

    dcos:service:marathon:user-marathon:services/production

In this example, a group is created that has access to the `user-marathon` instance `production/product1` subgroup, where `<service-type>` is `marathon`, `<service-name>` is `user-marathon`, `<namespace>` is `services`, and `<object-id>` is `/production/product1`.

    dcos:service:marathon:user-marathon:services/production/product1

In this example, a superuser group is created that has access to evertyhing in DCOS, where `<service-type>` is `superuser`, `<service-name>` is `superuser`, `<namespace>` is `superuser`, and `<object-id>` is `superuser`. This group is created by default.

    dcos:superuser

 [1]: https://www.ietf.org/rfc/rfc3986.txt