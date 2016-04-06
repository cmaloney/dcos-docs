---
UID: 56f984488993f
post_title: Configuring the CLI to use an HTTP Proxy
post_excerpt: ""
layout: page
published: true
menu_order: 16
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
If you use a proxy server to connect to the Internet, you can configure the CLI to use your proxy server.

**Prerequisites**

*   pip version 7.1.0 or greater 
*   The `http_proxy` and `https_proxy` environment variables are defined to use pip.

To configure a proxy for the CLI:

1.  From the CLI terminal, define the environment variables `http_proxy` and `https_proxy`:
    
        $ export http_proxy=’<http://your/proxy/here/>’
        $ export https_proxy=’<http://your/proxy/here/>’
        

2.  Define `no_proxy` for domains that you don’t want to use the proxy for:
    
        $ export no_proxy="127.0.0.1, localhost”