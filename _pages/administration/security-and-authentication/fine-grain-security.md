---
post_title: Fine grain security
layout: page
published: false
hide_from_navigation: true
hide_from_related: true
---

You can define fine-grained access to applications that are running in DCOS by defining advanced ACL groups. Fine-grained access provides multi-tenancy by isolating application teams and individual users. You can also control customized access to applications, for example read-only access. This access is administered on the backend by Admin Router and the native Marathon instance. 

Authorization is not secure, but provides user isolation. Anything running inside the cluster, and anyone SSH’d into it, has access to everything. In Mesos, for instance, you can see the logs and status of the apps.

When you add a service, you create an entry in the ACL.
- services/ = root access to everything
- services/production = a group
- services/production/prod1 = subgroup

Resource ID naming convention: https://mesosphere.atlassian.net/wiki/pages/viewpage.action?pageId=22544611
  
(dcos:service:marathon:user-marathon:services/dev-agility, full)

To create an advanced ACL group:

1.  Launch the DCOS web interface and login with your Admin username and password.

1.  Click on the **System** tab, select **Organization**, and then **Groups**.
 
1.  Click **New Group** button and provide a name for your group.

1.  Open the new group and click **Advanced ACLs**.

1.  Define your advanced ACL by providing Resource and Permission and then click **Add Rule**. 

    Resource: A resource has this format: `dcos:<service-type>:<service-name>:<namespace>/<object-id>`, where:
	
	- dcos: The required prefix.
	- <service-type>: The service type can be
		`service` - resources defined by a service/framework such as Marathon
		`adminrouter` - resources defined by Admin Router, such as locations
		`acs` - resources defined by the access control service
		`superuser` - superusers are allowed to do everything
	<service-name>:
		An [RFC 3986 (URI)](https://www.ietf.org/rfc/rfc3986.txt) compliant name. For example, `dcos:service:kafka`.
	<namespace>: The namespace can be:
		`services` - Marathon ACLs.
		`admin` - Admin Router ACLs.
		
	Permission: The type permissions assigned to the resource.
		`create` - Create an application.
		`delete` - Delete an application.
		`full` - Full CRUD access. This is the only available option for the `admin` namespace.
		`read` - Read-only access to an application.
		`update` - Update an application. 



Examples:
dcos:service:marathon:marathon:services/
Service Type: Marathon
Service Name: Marathon
Namespace: services 
Object ID: /
dcos:service:marathon:user-marathon:services/production
Service Type: Marathon
Service Name : user-marathon
Namespace: services
Object ID: /production
dcos:service:marathon:user-marathon:services/production/product1
Service Type: Marathon
Service Name: user-marathon
Namespace: services
Object ID: /production/product1
dcos:service:marathon:user-marathon:services/development
Service Type:Marathon
Service Name: user-marathon
Namespace: services
Object ID: /development
dcos:service:marathon:user-marathon:admin/leader
Service Type: Marathon
Service Name: user-marathon
Namespace: admin
Object ID: leader

This resource id for superuser is this. When present, the identity is allowed to do everything.
dcos:superuser
Service Type: superuser
Service Name: superuser
Namespace: superuser
Object ID: superuser
  
  
  
  
  