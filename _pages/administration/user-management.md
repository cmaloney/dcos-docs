---
UID: 573e5cd5c1eab
post_title: User Management
post_excerpt: ""
layout: page
published: true
menu_order: 2
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
Users are granted access to DC/OS and installed services by another authorized user. A default administrator user named `Bootstrap superuser` is created and added to the group named `Superuser group` during installation. The Superuser group has access to all DC/OS components and is where DC/OS administrator access is defined. Only members of the Superuser group have access to the DC/OS web interface.

# User management

To manage users:

1.  Launch the DCOS web interface and login with your Admin username and password.

2.  Click on the **Settings** -> **Users** tab and choose your action.
    
    ### Add local users
    
    *   From the **Users** tab, click **New User** and fill in the full name, username, and password. <!-- The full name supports unicode characters. The username supports all alphanumeric characters. Names can contain (A - Z), lowercase, supports Japanese, German, and English chars. -->
        
        <a href="/wp-content/uploads/2016/02/auth-enable-add-user2-1.gif" rel="attachment wp-att-3494"><img src="/wp-content/uploads/2016/02/auth-enable-add-user2-1-800x507.gif" alt="auth-enable-add-user2" width="800" height="507" class="alignnone size-large wp-image-3494" /></a>
    
    ### Add or Import remote users
    
    To provide external user access to DCOS, you can authenticate users that are defined in an LDAP 3 (RFC 4510) registry. You can then provide users with a single sign-on (SSO), granting them access the authorized services in your DCOS datacenter.
    
    When a user logs in and provides an LDAP user name and password, that information is used to connect to an LDAP server and authenticate a user. If successful, the DCOS authentication issues a session credential with the user’s distinguished name.
    
    LDAP users are uniquely identified by distinguished names (DN). To authenticate a user, a full DN and password are required. You can also use a DN template to allow users to enter a partial DN name. The partial name consists of the portion of their DN name that is unique to their accounts. The DN template converts the partial DN name to a full DN.
    
    1.  Click on the **System** -> **Organization** -> **External Directory** tab.
        
        1.  Click **Add Directory**.
            
            ![ldap user pane][1]
            
            **Host** Specify the hostname or IP address.
            
            **Port** Specify the port to use.
            
            **Lookup Username** Specify the username.
            
            **Lookup Password** Specify the password.
            
            **Distinguished Name Template** Specify the template to use for translating a username to a distinguished name. The distinguished name template is a static string that contains the `%(username)s` substring, which is replaced with the name the user provides when logging in. The template structure depends on your directory setup. Here's an example:
            
                cn=%(username)s,dc=los-pollos,dc=io
                uid=%(username)s,cn=users,cn=accounts,dc=demo1,dc=freeipa,dc=org
                uid=%(username)s,ou=users,dc=example,dc=com
                
            
            **Connection options:** By default, a plain text connection is opened and then \[STARTTLS\]\[1\] attempts to upgrade this plain text connection to an encrypted SSL/TLS connection. If this upgrade to encryption fails, the plain text connection continues. You can control this by choosing one of these options:
            
            *   **Use SSL/TLS socket for all connections** Use SSL/TLS socket for opening a connection.
            
            *   **Use startTLS for all connections** Abort connection if STARTTLS fails. If the upgrade to an encrypted SSL/TLS connection fails, the authentication operation is terminated.
    
    2.  Click **Add**. You can click **Test Connection** to validate that your connection. You should see this message: `Connection with LDAP server was successful`.
    
    3.  Remote users are not shown in the **User** tab until an initial remote user login is attempted. To add the remote user to the user list:
        
        1.  Click on your administrator username in the lower left corner and select **Sign Out**.
        2.  Log in to the DCOS web interface by using the remote user credentials. The initial remote user login will appear to fail, but will have added the remote user to the DCOS ACL. 
        3.  login with your Admin username and password and the remote user is now shown. 
    ### Modify users
    
    You can modify permissions, group membership, and passwords. You cannot modify the username.
    
    *   From the **Users** tab, click the user name and select your action. <a href="/wp-content/uploads/2016/02/auth-enable-modify.gif" rel="attachment wp-att-3495"><img src="/wp-content/uploads/2016/02/auth-enable-modify-800x509.gif" alt="auth-enable-modify" width="800" height="509" class="alignnone size-large wp-image-3495" /></a>
    ### Delete users
    
    1.  From the **Users** tab, select the user name and click **Actions -> Delete**. <a href="/wp-content/uploads/2016/02/auth-enable-delete-user.gif" rel="attachment wp-att-3498"><img src="/wp-content/uploads/2016/02/auth-enable-delete-user-800x510.gif" alt="auth-enable-delete-user" width="800" height="510" class="alignnone size-large wp-image-3498" /></a>
    2.  Click **Delete** to confirm the action.
    ### Switch users
    
    To switch users, you must log out of the current user and then back in as the new user.
    
    *   To log out of the DCOS web interface, click on your username in the lower left corner and select **Sign Out**.
        
        <a href="/wp-content/uploads/2016/02/auth-enable-logout-user.gif" rel="attachment wp-att-3533"><img src="/wp-content/uploads/2016/02/auth-enable-logout-user-800x430.gif" alt="auth-enable-logout-user" width="800" height="430" class="alignnone size-large wp-image-3533" /></a>
        
        You can now log in as another user.
    
    *   To log out of the DCOS CLI, enter the this command:
        
            $ dcos config unset core.dcos_acs_token
            Removed [core.dcos_acs_token]
            
        
        You can now log in as another user.

# Next steps

For more information on security, check out [Security and Authentication][2].

 [1]: /assets/images/auth-add-ldap-user.gif
 [2]: /administration/security/managing-authorization/