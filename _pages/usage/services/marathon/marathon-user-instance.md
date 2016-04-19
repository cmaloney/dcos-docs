---
UID: 56f984492e389
post_title: Adding a Marathon User Instance
post_excerpt: ""
layout: page
published: true
menu_order: 26
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
A native Marathon instance is installed as a part of the DC/OS installation. This tutorial creates a Marathon instance on top of the native Marathon to create separate user environments.

The Mesosphere Universe package repository in DC/OS makes it easy to deploy a user instance of Marathon via the Universe web UI.

![Marathon in Universe](/assets/images/mom-screen-0.png)

You'll also see how to add a Marathon user instance via the DC/OS CLI.

## Deploy via Mesosphere Universe

From the Universe page, locate the Marathon package and click Install Package.

![Install Dialog](/assets/images/mom-screen-1.png)

Click Advanced Installation to edit advanced settings before proceeding. Once the Marathon instance starts, you will see it on the Services page. From there, you can launch its web UI and deploy your applications. [Read more about Mesosphere Universe](https://dcos.io).


## Deploy via the CLI

Prerequisite

:   [Install the DC/OS CLI][1]

1.  Create a JSON configuration file, specify `marathon-alice` as the framework name, and save as `newuser.json`:
    
        {"marathon": {"framework-name": "marathon-alice" }}
        
    
    **Tip:** You must create separate JSON configuration files for each Marathon instance.

2.  From the DC/OS CLI, enter this command to install the Marathon instance:
    
        $ dcos package install --options=newuser.json marathon
        

3.  From the DC/OS web interface **Services** tab, click on the **marathon-alice** service name to navigate to the Marathon web interface.

4.  Optional: You can modify the DC/OS CLI configuration to point to the **marathon-alice** instance. This allows you to administer your Marathon instance by using the DC/OS CLI.
    
    1.  From the DC/OS CLI, set the `marathon.url` property to point to the **marathon-alice** instance, where `<hostname>` is the Marathon web interface hostname:
        
            $ dcos config set marathon.url http://<hostname>/service/marathon-alice/
            
    
    2.  Verify that the the `marathon.url` is set. The `marathon.url` takes precedence over the native Marathon in DCOS.
        
            $ dcos config show
            core.dcos_url=http://nodel-elasticl-1xyz-1940784093.us-west-2.elb.amazonaws.com
            core.email=youremail@email.io
            core.reporting=True
            core.token=a547c734ed81247d0203ce238a5a07ac012b59f8d7f89ed539e5110557548152
            marathon.url=http://alicenodel-elasticl-1xyz-1940784093.us-west-2.elb.amazonaws.com/service/marathon-alice/
            package.cache=/Users/alice/.dcos/cache
            package.sources=['https://github.com/mesosphere/universe/archive/version-1.x.zip']
            
        
        **Tip:** You can switch back to the native Marathon instance by specifying `dcos config unset marathon.url`.

### Next Steps

After you have your Marathon instance up and running, you can try [Deploying a Containerized App on a Public Node][2].

 [1]: /usage/cli/install/
 [2]: /usage/tutorials/containerized-app/
