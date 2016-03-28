---
UID: 56f9844812a6f
post_title: Changing the CLI master node
post_excerpt: ""
layout: page
published: true
menu_order: 105
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
You can change the master node that the CLI is connected to.

This can be useful if the hostname for your master has changed or to point the CLI to a different DCOS cluster entirely. The hostname is specified in the `dcos_url` option in the `~./dcos/dcos.toml` configuration file.

1.  Enter this command to view the DCOS configuration file. In this example the hostname is `http://bobs.ramps.com`.
    
        $ dcos config show
        core.dcos_url=http://bobs.ramps.com
        core.email=jdoe@bobs.ramps.com
        core.reporting=False
        core.ssl_verify=false
        core.timeout=5
        

2.  Specify the new hostname as `http://roberts.ramps.com`:
    
        $ dcos config set core.dcos_url http://roberts.ramps.com
        

3.  You can verify that the hostname has been updated with this command:
    
        $ dcos config show core.dcos_url
        http://roberts.ramps.com
        

For more information about the DCOS configuration settings, see the [documentation][1].

 [1]: /cli/#scrollNav-2