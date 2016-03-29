---
post_title: Fine grain security
layout: page
published: false
hide_from_navigation: true
hide_from_related: true
---

Overview:

You can define fine-grained access to applications that are running in DCOS. Fine-grained access allows multi-tenancy by isolating application teams or individual users (/home/some-user/), and custom access (e.g read-only access to a group). This access is administered on the backend by Admin Router and the native Marathon instance. 

Since all current application packages do not have the owner field, they will default to the principal of the user creating an app.

Authorization isn’t secure. It just isolates users. Anything running inside the cluster, and anyone SSH’d into it, has access to everything. In Mesos, for instance, you can see the logs and status of the apps.

The DCOS Admin Router will handle HTTP requests authentication by itself or delegate to another existing Identity Authentication System (flat file or LDAP etc) which is able to authenticate requests (e.g., principal/user name, password/token). It will then add an Authorization header to the request  and forward it to Marathon. That header will include JSON Web Token containing the user principal.

When you add a service, you create an entry in the ACL.
services/ = root access to everything
services/production = a group
services/production/prod1 = subgroup
Groups: you can put users in there, then give the groups access to this and that.
 So the groups get the permissions, and then you put the users in groups, rather than assign each user permissions. This makes it easier to manage your permissions, so that’s what we suggest.





Resource ID naming convention: https://mesosphere.atlassian.net/wiki/pages/viewpage.action?pageId=22544611
  
(dcos:service:marathon:user-marathon:services/dev-agility, full)

1.  Click System -> Organization -> Groups -> New Group
1.  Open group -> Advanced ACLs -> Add Rule. A resource ID has this format: dcos:<type>:<name>[:<optional>], where:
	dcos: The required prefix.
	<type>: 
		service - resources defined by a service/framework such as Marathon
		adminrouter - resources defined by Admin Router, such as locations
		acs - resources defined by the access control service
		superuser - superusers are allowed to do everything
	<name>:
	
1.  



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
  
  
  
  
  