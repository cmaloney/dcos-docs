---
post_title: Using Jenkins on DC/OS
nav_title: Jenkins
---

[Jenkins][jenkins-website] is a popular, open source continuous integration
(CI) automation server and framework with hundreds of plugins (GitHub, Docker,
Slack, etc) available. Running Jenkins on DC/OS allows you to scale your CI
infrastructure by dynamically creating and destroying Jenkins agents as demand
increases or decreases, and enables you to avoid the statically partitioned
infrastructure typical of a traditional Jenkins deployment.

### Time Estimate

Varies (up to 45 minutes)

### Target Audiences

Operators, Application administrators, Quality / Release engineers, CI/CD
administrators

### Scope

In the following tutorial, you'll learn about how to use Jenkins on DC/OS.
You'll learn how to install Jenkins, and then how to use it to build and deploy
a Docker image on Marathon.

### Table of Contents

  * Prerequisites
  * Installing Jenkins in a development environment
  * Installing Jenkins in production
    * Creating a file share on Microsoft Azure
  * Building a Docker image and deploying it to Marathon
  * Uninstalling Jenkins
  * Further reading

## Preparation

Assuming you already have a DC/OS cluster up and running, you'll first want to
[install a user-specific Marathon instance][marathon-service-docs]. This will
serve as the deployment platform for any user-created applications that are
deployed by Jenkins.

```bash
$ dcos package install marathon
We recommend a minimum of one node with at least 2 CPU shares and 1GB of RAM
available for the Marathon DC/OS Service.
Continue installing? [yes/no] yes
Installing Marathon app for package [marathon] version [0.15.3]
Marathon DC/OS Service has been successfully installed!

    Documentation: https://mesosphere.github.io/marathon
    Issues: https:/github.com/mesosphere/marathon/issue
```

This Marathon instance will appear in the DC/OS dashboard as `marathon-user`.

Jenkins works by persisting information about its configuration and build
history as files on disk. Therefore, we have two options for deploying
Jenkins on DC/OS: pin it to a single node, or use a network file system such
as NFS, CIFS, and so on.

## Installing Jenkins in a development environment

If you only want to run Jenkins in a development environment, it's trivial
to pin it to a single agent in the DC/OS cluster. Create the file
`options.json` with the configuration below:

```bash
$ cat options.json
{
    "jenkins": {
        "pinned-hostname": "10.100.100.88"
    }
}
```

*Tip: for a complete list of the configuration options available for the
Jenkins package, see the [Jenkins package definition in the Mesosphere
Universe][mesosphere-universe-jenkins].*

Once you create `options.json`, you can then install Jenkins by running the
following command:

```bash
$ dcos package install jenkins --options=options.json
WARNING: Jenkins on DC/OS is currently in BETA. There may be bugs, incomplete
features, incorrect documentation, or other discrepancies.

If you didn't provide a value for `host-volume` in the CLI,
YOUR DATA WILL NOT BE SAVED IN ANY WAY.

Continue installing? [yes/no] yes
Installing Marathon app for package [jenkins] version [0.2.3]
Jenkins has been installed.
```

Once ready, Jenkins will appear as a service in the DC/OS dashboard.

## Installing Jenkins in production

As mentioned previously, running Jenkins in a production environment will
require that each machine in the cluster has an external volume mounted at the
same location. This external volume can be backed by any number of systems,
including NFS, CIFS, Ceph, and others. This will allow Jenkins to persist data
to the external volume while still being able to run on any agent in the
cluster, preventing against outages due to machine failure.

If you already have a mount point, great! Create an `options.json` file that
resembles the following example:

```bash
$ cat options.json
{
    "jenkins": {
        "framework-name": "jenkins-prod",
        "host-volume": "/mnt/jenkins",
        "cpus": 2.0,
        "mem": 4096
    }
}
```

Then, install Jenkins by running the following command:

```bash
$ dcos package install jenkins --options=options.json
```

If you don't have a file share set up and are looking for a solution, continue
with the next section for instructions on how to set up a CIFS share using
Microsoft Azure.

### Creating a file share on Microsoft Azure
First, you need to create a [Storage Account][azure-storage-account] in the
same resource group in which you've launched your DC/OS cluster.

In this particular example, let's create the storage account `mh9storage` in
the resource group `mh9`:

![Azure Portal: Storage Account](img/azure-portal-storage.png)

Now, create a file share. In this example, I used `jenkins`:

![Azure Portal: File Service](img/azure-portal-storage-fileshare.png)

Next, login to the DC/OS master node. To determine the master, look up the SSH
connection string labeled `SSHMASTER0` in the `Outputs` section of the
`Microsoft.Template`.

![Azure Portal: Deployment Output](img/azure-portal-deployment-output.png)

Next, add the private SSH key locally:

```bash
$ ssh-add ~/.ssh/azure
Identity added: /Users/mhausenblas/.ssh/azure (/Users/mhausenblas/.ssh/azure)
```

And now, login to the master node. Note that the `-L 8000:localhost:80` is
forwarding port 8000 from your local machine to port 80 on the remote host.

```bash
$ ssh azureuser@jenkinsmgmt.westus.cloudapp.azure.com -A -p 2200 \
    -L 8000:localhost:80
```

On this node you can now [mount the File Share][mount-file-share-azure] we
created in the previous step. First, let's make 100% sure that the CIFS mount
utils are available:

```bash
$ sudo apt-get update && sudo apt-get -y install cifs-utils
```

Now we can mount the file share:

```bash
azureuser@dcos-master-415F65E0-0:~$ sudo mkdir -p /mnt/jenkins
azureuser@dcos-master-415F65E0-0:~$ sudo mount -t cifs    \
  //mh9storage.file.core.windows.net/jenkins /mnt/jenkins \
  -o vers=3.0,username=REDACTED,password=REDACTED,dir_mode=0777,file_mode=0777
```

Be sure to replace the `REDACTED` value for the `username` and `password`
options with your username and password. Note that the value for `password` is
`KEY2` from `Access keys`, as shown here:

![Azure Portal: Storage Account Access Keys](img/azure-portal-storage-accesskeys.png)

To check if the file share works, we upload a test file via the Azure portal:

![Azure Portal: Storage File Upload](img/azure-portal-storage-fileupload.png)

If all is well, you should be able to list the contents of the mounted file
share on the DC/OS master node:

```bash
azureuser@dcos-master-415F65E0-0:~$ ls -al /mnt/jenkins
total 1
-rwxrwxrwx 1 root root 19 Mar 20 11:21 test.txt
```

Finally, using the pssh tool, configure each of the DC/OS agents to mount the file share.

```bash
$ sudo apt-get install pssh
$ cat pssh_agents
10.0.0.4
10.0.0.5
10.32.0.4

$ parallel-ssh -O StrictHostKeyChecking=no -l azureuser -h pssh_agents "if [ ! -d "/mnt/jenkins" ]; then mkdir -p "/mnt/jenkins" ; fi"
$ parallel-ssh -O StrictHostKeyChecking=no -l azureuser -h pssh_agents "mount -t cifs //mh9storage.file.core.windows.net/jenkins /mnt/jenkins -o vers=3.0,username=REDACTED,password=REDACTED,dir_mode=0777,file_mode=0777"
```

## Building a Docker image and deploying it to Marathon

*Note: for this example, I'll assume you already have your own Docker Hub
account or access to a Docker image registry.*

By default, the Jenkins package is configured with a Docker-in-Docker agent
that allows you to build Docker images on top of DC/OS. Nothing else is needed
on your part!

Mesosphere maintains an open source Marathon plugin for Jenkins, which allows
you to easily deploy an application to Marathon. To install it, perform the
following steps:

  1. Download the .hpi file for the latest Marathon plugin here:
  https://github.com/mesosphere/jenkins-marathon-plugin/releases
  2. Upload the `.hpi` plugin file via the "Advanced" tab within the Jenkins
  plugin manager:
  ![Jenkins plugin installation](img/jenkins-plugin-install.png)
  3. Restart Jenkins to load the new plugin.

Next, you'll configure a Jenkins job that clones a repository, builds the
image, pushes it to Docker Hub, and deploys it to Marathon.

![Configure Git repository](img/jenkins-scm-repo.png)

For the build step, you may use (or adapt) the following build script:

```bash
#!/bin/bash
IMAGE_NAME="${DOCKER_USERNAME}/${JOB_NAME}:${GIT_COMMIT}"

docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD} -e ${DOCKER_EMAIL}
docker build -t $IMAGE_NAME .
docker push $IMAGE_NAME
```

Finally, configure a post-build step using the Marathon plugin:

![Marathon deployment post-build step](img/jenkins-marathon-post-build-step.png)

An example of a Marathon deployment follows:

![Marathon deployment post-build configuration](img/jenkins-marathon-post-build-config.png)

## Uninstalling Jenkins

Using the DC/OS CLI, run the following command:

```
$ dcos package uninstall jenkins
```

## Further Reading

  * [Jenkins project website][jenkins-website] (jenkins-ci.org)
  * [Jenkins service documentation][jenkins-service-docs] (mesosphere.com)
  * [Marathon service documentation][marathon-service-docs] (mesosphere.com)
  * [Mesos plugin for Jenkins][jenkins-mesos-plugin] (github.com)

[azure-storage-account]: https://portal.azure.com/#create/Microsoft.StorageAccount-ARM
[jenkins-mesos-plugin]: https://github.com/jenkinsci/mesos-plugin
[jenkins-service-docs]: https://docs.mesosphere.com/manage-service/jenkins/
[jenkins-website]: https://jenkins-ci.org
[marathon-service-docs]: https://docs.mesosphere.com/manage-service/marathon/
[mesosphere-universe-jenkins]: https://github.com/mesosphere/universe/tree/version-2.x/repo/packages/J/jenkins
[mount-file-share-azure]: https://azure.microsoft.com/en-us/documentation/articles/storage-how-to-use-files-linux/
