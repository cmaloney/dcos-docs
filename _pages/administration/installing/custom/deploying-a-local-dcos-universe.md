---
UID: 56f98447aaf69
post_title: Deploying a local package repository
post_excerpt: ""
layout: page
published: true
menu_order: 205
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
You can install and run DC/OS services on a datacenter without internet access with a local Universe. You can install a local Universe that includes the default packages (easiest), or select your own set of local Universe packages (advanced).

#### Prerequisites

*   DC/OS cluster
*   8\.5 GB of disk space

## Installing the default Universe packages

1.  Download the [local-universe][1] container to each of your masters.
    
    **Tip:** The `local-universe.tar.gz` file size is 2 GB or more.

2.  Load the container into the local Docker instance on each of your master nodes:
    
        $ docker load < local-universe.tar.gz
        

3.  Add the [`dcos-local-universe-http.service`][2] definition to each of your masters at `/etc/systemd/system/dcos-local-universe-http.service` and then start it.
    
        $ cp dcos-local-universe-http.service /etc/systemd/system/dcos-local-universe-http.service
        $ systemctl daemon-reload
        $ systemctl start dcos-local-universe-http
        

4.  Add the [`dcos-local-universe-registry.service`][3] definition to each of your masters at `/etc/systemd/system/dcos-local-universe-registry.service` and then start it.
    
        $ cp dcos-local-universe-registry.service /etc/systemd/system/dcos-local-universe-registry.service
        $ systemctl daemon-reload
        $ systemctl start dcos-local-universe-registry
        

5.  Remove the native DC/OS Universe repositories from the host that you have the DC/OS CLI installed on (alternatively, these can be removed from the DC/OS UI under **System** -> **Repositories**).
    
        $ dcos package repo remove Universe
        $ dcos package repo remove Universe-1.7
        

6.  Add the local repository by using the DC/OS CLI.
    
        $ dcos package repo add local-universe http://master.mesos:8082/universe.zip
        

7.  To pull from this new repository, you must setup the Docker daemon on every agent to have a valid SSL certificate. To do this, on every agent in your cluster, run the following:
    
        $ mkdir -p /etc/docker/certs.d/master.mesos:5000
        $ curl -o /etc/docker/certs.d/master.mesos:5000/ca.crt http://master.mesos:8082/certs/domain.crt
        $ systemctl restart docker
        
    
    **Tip:** You can use the instructions for insecure registries, instead of this step, however we don't recommend this.
    
    ### FAQ
    
    *   **I can't install CLI subcommands**
        
        Packages are hosted at `master.mesos:8082`. If you cannot resolve or connect to `master.mesos:8082` from your DC/OS CLI install, you cannot install CLI subcommands. If you can connect to port 8082 on your masters, add the IP for one of the masters to `/etc/hosts`.
    
    *   **The images are broken**
        
        All Universe components are hosted inside of your cluster, including the images. The components are served up by `master.mesos:8082`. If you have connectivity to that IP, you can add it to `/etc/hosts` and get the images working.
    
    *   **I don't see the package I was looking for**
        
        By default, only the `selected` packages are bundled. If you'd like to get something else, use the build your own [instructions][4].

## <a name="build"></a>Installing a selected set of Universe packages

With this method you build a `universe-base` container then compile the Universe container yourself. Both nginx and the Docker registry are bundled into the `universe-base` container.

1.  Build the `universe-base` container:
    
        $ sudo make base
        
    
    Now that you've built the `universe-base` container, you can create a `local-universe` container.

2.  Optional: Modify the Universe [`makefile`][5] to include only selected packages. To minimize the container size and download time, you can select only what you need. If you do not modify the `makefile` all default Universe packages will be included.

3.  Build your local Universe container:
    
        $ sudo make local-universe

 [1]: https://downloads.mesosphere.com/universe/public/local-universe.tar.gz
 [2]: https://raw.githubusercontent.com/mesosphere/universe/version-2.x/local/dcos-local-universe-http.service
 [3]: https://raw.githubusercontent.com/mesosphere/universe/version-2.x/local/dcos-local-universe-registry.service
 [4]: #build
 [5]: https://github.com/mesosphere/universe/blob/version-2.x/Makefile