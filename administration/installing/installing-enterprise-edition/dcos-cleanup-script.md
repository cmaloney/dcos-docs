---
post_title: DCOS cleanup script
menu_order: 209
---
Sometimes, you can get your hosts into a bad spot and want to start over. Instead of creating brand new instances, you can run this cleanup script and get rid of all the DCOS specific things.

To run the DCOS cleanup script:

    `$ sudo -i /opt/mesosphere/bin/pkgpanda uninstall
    $ sudo rm -rf /opt/mesosphere
    `