---
UID: 56f984477b877
post_title: DC/OS cleanup script
post_excerpt: ""
layout: page
published: true
menu_order: 209
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
Sometimes, you can get your hosts into a bad spot and want to start over. Instead of creating brand new instances, you can run this cleanup script and get rid of all the DC/OS specific things.

To run the DC/OS cleanup script:

    $ sudo -i /opt/mesosphere/bin/pkgpanda uninstall
    $ sudo rm -rf /opt/mesosphere
    

**Tip:** This doesn't completely wipe out everything on the host.