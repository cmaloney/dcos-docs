
---
UID: 5715531b80d8d
post_title: The Framework Cleaner Script
post_excerpt: ""
layout: page
published: true
menu_order: 0
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
<<<<<<< HEAD

=======
>>>>>>> 6e3dcd827ccf509ccdb4f2c10dd408bbb7aacdf5
The framework cleaner docker image, `mesosphere/janitor`, uses cluster-internal endpoints for interacting with DCOS services, avoiding the need to authenticate itself.

# Run the Script

Enter the following as a marathon task (JSON Mode enabled). Replace the values passed to `-r`/`-p`/`-z` according to what needs to be cleaned up:

    {
      "id": "janitor",
      "cmd": "/janitor.py -r sample-role -p sample-principal -z sample",
      "cpus": 1,
      "mem": 128,
      "disk": 1,
      "instances": 1,
      "container": {
        "docker": {
          "image": "mesosphere/janitor:latest",
          "network": "HOST"
        },
        "type": "DOCKER"
      }
    }
    

Below are some examples of default configurations. These will vary depending on selected task name, etc.

*   Cassandra:
    
    *   Default: `-r cassandra_role -p cassandra_principal -z cassandra`
    *   Custom name: `-r <name>_role -p <name>_principal -z <name>`

*   Kafka:
    
    *   Default: `-r kafka-role -p kafka-principal -z kafka`
    *   Custom name: `-r <name>-role -p <name>-principal -z <name>`

When the framework cleaner has completed its work, it will automatically remove itself from Marathon to ensure that it's only run once. This removal will often result in a `TASK_KILLED` outcome for the janitor task. It will also quickly disappear from both the Marathon web interface and the Dashboard.

# Verify the Outcome

To view the script's outcome, go to Mesos (http://your-cluster.com/mesos) and look at the task's `stdout` content. If `stdout` lacks content, run the following command manually:

    # Determine id of agent which ran the Docker task. This is an example:
    
    your-machine$ dcos node ssh --master-proxy --mesos-id=c62affd0-ce56-413b-85e7-32e510a7e131-S3
    
    agent-node$ docker ps -a CONTAINER ID IMAGE COMMAND ... 828ee17b5fd3 mesosphere/janitor:latest /bin/sh -c /janito ...
    
    agent-node$ docker logs 828ee17b5fd3
