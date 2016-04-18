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
Follow these instructions to download and configure a local <a href="http://mesosphere.github.io/universe/" target="_blank">DCOS Universe</a>. After downloading the Universe components, you can install and run DCOS services on a datacenter without internet access.

#### Prerequisites

*   DCOS cluster
*   8\.5 GB of disk space

To install the DCOS local Universe:

1.  Download the package binaries as a single tarball. This package includes regular tarballs and Docker images.
    
        $ wget https://s3-us-west-2.amazonaws.com/local-universe-archive/dcos-services.tar.gz
        
    
    There is a tarball named `docker-images.tar` that contains all of the required Docker images. Here is list of the tags and image IDs:
    
    | REPOSITORY                        | TAG         | IMAGE ID     |
    | --------------------------------- | ----------- | ------------ |
    | docker.io/mesosphere/marathon     | v0.11.1     | 5e187be16235 |
    | docker.io/arangodb/arangodb-mesos | latest      | 17881b9777fe |
    | docker.io/mesosphere/chronos      | chronos-... | 99852a4a1a6e |
    | docker.io/mesosphere/spark        | 1\.5.0-...  | 23b1cfe8e04a |

2.  Extract the tarball:
    
        $ tar xvf dcos-services.tar.gz
        

3.  Host the binaries in an artifact store.
    
    1.  For the Docker images, you can do the following:
        
            $ docker run -d -p 5000:5000 --restart=always --name registry registry:2
            $ docker load < dcos-services/docker-images.tar # This will take a long time.
            $ for img in $(docker images | awk '{ print $1 ":" $2 }' | grep mesos); do
                img_tag=$(ip addr show eth0 | grep -Eo '[0-9]{1,3&#92;[0-9]{1,3&#92;[0-9]{1,3&#92;[0-9]{1,3}' | head -1):5000/$img
                docker tag $img $img_tag
                docker push $img_tag
              done
            
        
        **Important:** You must have all of the Docker daemons in your cluster setup with `--insecure-registry`. For more information, see the [Docker Registry documentation][1].
    
    2.  Serve the other artifacts with this command:
        
            $ docker run -p 80:80 --name some-nginx -v $(pwd)/dcos-services:/usr/share/nginx/html:ro -d nginx
            

4.  On a machine with internet access, clone the Mesosphere Universe repository:
    
        $ git clone https://github.com/mesosphere/universe.git
        $ cd universe
        $ git checkout a0b1317deccb158b11eb7fca942e17460f3640d2
        
    
    **Tip:** The remaining steps do not require internet access.

5.  Update your local Universe packages and then go to the [next step][2].
    
    *   [Cassandra][3]
    *   [Chronos][4]
    *   [Kafka][5]
    *   [HDFS][6]
    *   [Marathon][7]
    *   [Spark][8]
    ### <a name="arangodb"></a>ArangoDB
    
    1.  In the `universe/repo/packages/A/arangodb/2/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "assets": {
                "container": {
                  "docker": {
                    "17881b9777fe": "<local-docker-hostname>/agangodb/arangodb-mesos:latest"
                  }
                }
              },
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/arangodb_small.png",
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/arangodb_medium.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/arangodb_large.png"
              }
            }
            
    
    ### <a name="cassandra"></a>Cassandra
    
    1.  In the `universe/repo/packages/C/cassandra/3/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "assets": {
                "uris": {
                  "cassandra-mesos-0-2-0-1-tar-gz": "http://<local-http-hostname>/<path-to-file>/cassandra-mesos-0.2.0-1.tar.gz",
                  "jre-7u76-linux-x64-tar-gz": "http://<local-http-hostname>/<path-to-file>/jre-7u76-linux-x64.tar.gz"
                }
              },
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/cassandra-small.png",
                "icon-medium": "https://<local-http-hostname>/<path-to-file>/cassandra-medium.png",
                "icon-large": "https://<local-http-hostname>/<path-to-file>/cassandra-large.png"
              }
            }
            
    
    ### <a name="chronos"></a>Chronos
    
    1.  In the `universe/repo/packages/C/chronos/3/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "assets": {
                "container": {
                  "docker": {
                    "99852a4a1a6e": "<local-docker-hostname>/mesosphere/chronos:chronos-2.4.0-0.1.20150828104228.ubuntu1404-mesos-0.23.0-1.0.ubuntu1404"
                  }
                }
              },
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/icon-service-chronos-small.png",
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/icon-service-chronos-medium.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/icon-service-chronos-large.png"
              },
            }
            
    
    ### <a name="kafka"></a>Kafka
    
    1.  In the `universe/repo/packages/K/kafka/2/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "assets": {
                "uris": {
                  "jre-7u79-openjdk-tgz": "http://<local-http-hostname>/<path-to-file>/jre-7u79-openjdk.tgz",
                  "kafka_2-10-0-8-2-1-tgz": "http://<local-http-hostname>/<path-to-file>/kafka_2.10-0.8.2.1.tgz",
                  "kafka-mesos-0-9-2-0-jar": "http://<local-http-hostname>/<path-to-file>/kafka-mesos-0.9.2.0.jar"
                }
              },
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/icon-small.png",
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/icon-medium.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/icon-large.png",
                "screenshots": ["http://<local-http-hostname>/<path-to-file>/screen-0-kafka.png"]
              }
            }
            
    
    ### <a name="hdfs"></a>HDFS
    
    1.  In the `universe/repo/packages/H/hdfs/4/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/icon-service-hdfs-small.png",
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/icon-service-hdfs-medium.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/icon-service-hdfs-large.png"
              },
              "assets": {
                "uris": {
                  "hdfs-mesos-0-1-5-tgz": "http://<local-http-hostname>/<path-to-file>/hdfs-mesos-0.1.5.tgz",
                  "jre-7u76-linux-x64-tar-gz": "http://<local-http-hostname>/<path-to-file>/jre-7u76-linux-x64.tar.gz"
                }
              }
            }
            
    
    ### <a name="marathon"></a>Marathon
    
    1.  In the `universe/repo/packages/M/marathon/8/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "images": {
                "icon-small": "http://<local-http-hostname>/<path-to-file>/icon-service-marathon-small.png",
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/icon-service-marathon-medium.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/icon-service-marathon-large.png"
              },
              "assets": {
                "container": {
                  "docker": {
                    "5e187be16235": "<local-docker-hostname>/mesosphere/marathon:v0.11.1"
                  }
                }
              }
            }
            
    
    ### <a name="spark"></a>Spark
    
    1.  In the `universe/repo/packages/S/spark/5/resource.json` file, replace all of the URLs with the locally hosted URLs. For example:
        
            {
              "assets": {
                "uris": {
                  "log4j-properties": "http://<local-http-hostname>/<path-to-file>/log4j.properties"
                },
                "container": {
                  "docker": {
                    "23b1cfe8e04a": "<local-docker-hostname>/mesosphere/spark:1.5.0-multi-roles-v2-bin-2.4.0"
                  }
                }
              },
              "images": {
                "icon-medium": "http://<local-http-hostname>/<path-to-file>/icon-service-spark-medium.png",
                "icon-small": "http://<local-http-hostname>/<path-to-file>/icon-service-spark-small.png",
                "icon-large": "http://<local-http-hostname>/<path-to-file>/icon-service-spark-large.png"
              }
            }
            

6.  <a name="next"></a>Set the DCOS CLI package source to your local Universe repository:
    
        $ dcos package repo add <repo-name> '["file:///<path-to-cloned-repo>"]'
        

7.  Turn off CLI usage and exception reporting:
    
        $ dcos config set core.reporting false
        

8.  Install DCOS services. For example, to install Cassandra:
    
        dcos package install cassandra

 [1]: https://docs.docker.com/registry/
 [2]: #next
 [3]: #cassandra
 [4]: #chronos
 [5]: #kafka
 [6]: #hdfs
 [7]: #marathon
 [8]: #spark