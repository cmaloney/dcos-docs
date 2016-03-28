---
UID: 56f9844809d59
post_title: Managing DCOS Marathon
post_excerpt: ""
layout: page
published: true
menu_order: 12
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
# Specifying the Marathon environment variable as a CLI command

In this example, the [Marathon `env` variable][1] is updated by specifying a JSON string in the command.

1.  Specify this CLI command with the JSON string included:
    
        dcos marathon app update test-app env='{"APISERVER_PORT":"25502"}'
        

2.  Run the command below to see the result of your update:
    
        dcos marathon app show test-app | jq '.env'
        

# Specifying the Marathon environment variable in a JSON file

In this example, the [Marathon `env` variable][1] is updated by specifying a JSON file in the command.

1.  Save the existing environment variables to a file:
    
        dcos marathon app show test-app | jq .env >env_vars.json
        
    
    The file will look like this:
    
            { "SCHEDULER_DRIVER_PORT": "25501", }
        

2.  Edit the `env_vars.json` file. Make the JSON a valid object by enclosing the file contents with `{ "env" :}` and add your update:
    
        { "env" : { "APISERVER_PORT" : "25502", "SCHEDULER_DRIVER_PORT" : "25501" } }
        

3.  Specify this CLI command with the JSON file specified:
    
        dcos marathon app update test-app < env_vars.json
        
    
    View the results of your update:
    
           dcos marathon app show test-app | jq '.env'
        

# Displaying completed Marathon tasks

In this example, all completed HDFS tasks are shown:

    $ dcos task --completed hdfs
     NAME  USER  STATE                      ID                    
     hdfs  root    F    hdfs.6b18a882-00a5-11e5-9926-56847afe9799 
     hdfs  root    K    hdfs.6eacf2d3-00a5-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.71165c45-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.74125e36-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.770e6027-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.7b3bdd38-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.7f698159-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.809b71aa-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.82fe19cb-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.86928b2c-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.8ac02f4d-00a6-11e5-9926-56847afe9799 
     hdfs  root    F    hdfs.8e54528e-00a6-11e5-9926-56847afe9799

 [1]: https://mesosphere.github.io/marathon/docs/task-environment-vars.html