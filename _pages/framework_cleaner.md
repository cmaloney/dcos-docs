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
The framework cleaner docker image, `mesosphere/janitor`, simplifies the process of removing your service instance from Zookeeper and destroying all the data associated with it. `mesosphere/janitor` uses cluster-internal endpoints for interacting with DC/OS services, avoiding the need to authenticate itself.

There are two ways to run the framework cleaner script. The preferred method is via the DC/OS CLI. If the CLI is unavailable, you can also run the image as a self-deleting Marathon task.

# Configuration

The script takes the following flags:
- `-r`: The role of the resources to be deleted
- `-p`: The principal of the resources to be deleted
- `-z`: The configuration zookeeper node to be deleted

These are some examples of default configurations (these will vary depending on selected task name, etc):
    - Cassandra:
        - Default: `-r cassandra_role -p cassandra_principal -z cassandra`
        - Custom name: `-r <name>_role -p <name>_principal -z <name>`
    - Kafka:
        - Default: `-r kafka-role -p kafka-principal -z kafka`
        - Custom name: `-r <name>-role -p <name>-principal -z <name>`

# Run from the DC/OS CLI

Connect to the leader and start the script:

1. Open an SSH session to the cluster leader.

        your-machine$ dcos node ssh --master-proxy --leader

1. Run the `mesosphere/janitor` image with the role, principal, and zookeeper nodes that were configured for your service:

        leader$ docker run mesosphere/janitor /janitor.py -r sample-role -p sample-principal -z sample-zk

# Run from Marathon

From the Marathon web interface, use the JSON editor to add the following as a Marathon task. Replace the values passed to `-r`/`-p`/`-z` according to what needs to be cleaned up.

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
    
When the framework cleaner has completed its work, it will automatically remove itself from Marathon to ensure that it's only run once. This removal will often result in a `TASK_KILLED` or even a `TASK_FAILED` outcome for the janitor task, even if it finished successfully. The janitor task will also quickly disappear from both the Marathon web interface and the Dashboard.

# Verify the Outcome

To view the script's outcome, go to Mesos (http://your-cluster.com/mesos) and look at the task's `stdout` content. If `stdout` lacks content, run the following command manually:

    # Determine id of agent which ran the Docker task. This is an example:
    
    your-machine$ dcos node ssh --master-proxy --mesos-id=c62affd0-ce56-413b-85e7-32e510a7e131-S3
    
    agent-node$ docker ps -a
    CONTAINER ID        IMAGE                       COMMAND             ...
    828ee17b5fd3        mesosphere/janitor:latest   /bin/sh -c /janito  ...
    
    agent-node$ docker logs 828ee17b5fd3
    
# Sample Result

Here's an example of the output for a successful run for a Cassandra installation:

    your-machine$ dcos node ssh --master-proxy --leader

    leader-node$ docker run mesosphere/janitor /janitor.py -r cassandra_role -p cassandra_principal -z cassandra
    [... docker download ...]
    Master: http://leader.mesos:5050/master/ Exhibitor: http://leader.mesos:8181/ Role: cassandra_role Principal: cassandra_principal ZK Path: cassandra
    
    Destroying volumes...
    Mesos version: 0.28.1 => 28
    Found 1 volume(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S5, deleting...
    200 
    Found 1 volume(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S4, deleting...
    200 
    No reserved resources for any role on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S3
    No reserved resources for any role on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S2
    Found 1 volume(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S1, deleting...
    200 
    No reserved resources for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S0. Known roles are: [slave_public]
    
    Unreserving resources...
    Found 4 resource(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S5, deleting...
    200 
    Found 4 resource(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S4, deleting...
    200 
    No reserved resources for any role on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S3
    No reserved resources for any role on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S2
    Found 4 resource(s) for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S1, deleting...
    200 
    No reserved resources for role 'cassandra_role' on slave 3ce447e3-2894-4c61-bd0f-be97e4d99ee9-S0. Known roles are: [slave_public]
    
    Deleting zk node...
    Successfully deleted znode 'cassandra' (code=200), if znode existed.
    Cleanup completed successfully.

If you run the script via Marathon, you will also see the following output:

    Deleting self from Marathon to avoid run loop: /janitor
    Successfully deleted self from marathon (code=200): /janitor