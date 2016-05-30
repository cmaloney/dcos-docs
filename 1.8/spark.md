---
layout: page
post_title: Spark
menu_order: 0
---
<h1>Overview</h1>

<p>Spark is a fast and general-purpose cluster computing system for big data. It provides high-level APIs in Scala, Java, Python, and R, and an optimized engine that supports general computation graphs for data analysis. It also supports a rich set of higher-level tools including Spark SQL for SQL and DataFrames, MLlib for machine learning, GraphX for graph processing, and Spark Streaming for stream processing. For more information, see the <a href="http://spark.apache.org/documentation.html">Apache Spark documentation</a>.</p>

<p>DC/OS Spark includes:</p>

<ul>
<li><a href="http://spark.apache.org/docs/latest/running-on-mesos.html#cluster-mode">Mesos Cluster Dispatcher</a></li>
<li><a href="http://spark.apache.org/docs/latest/monitoring.html#viewing-after-the-">Spark History Server</a></li>
<li>DC/OS Spark CLI</li>
</ul>

<h2>Benefits</h2>

<ul>
<li>Utilization: DC/OS Spark leverages Mesos to run Spark on the same cluster as other DC/OS services</li>
<li>Improved efficiency</li>
<li>Simple Management</li>
<li>Multi-team support</li>
<li>Interactive analytics through notebooks</li>
<li>UI integration</li>
<li>Security</li>
</ul>

<h2>Features</h2>

<ul>
<li>Multiversion support</li>
<li>Run multiple Spark dispatchers</li>
<li>Run against multiple HDFS clusters</li>
<li>Backports of scheduling improvements</li>
<li>Simple installation of all Spark components, including the dispatcher and the history server</li>
<li>Integration of the dispatcher and history server</li>
<li>Zeppelin integration</li>
<li>Kerberos and SSL support</li>
</ul>

<h2>Related Services</h2>

<ul>
<li><a href="https://gitsync-mesosphere-documentation.pantheonsite.io/manage-service/hdfs/">HDFS</a></li>
<li><a href="https://gitsync-mesosphere-documentation.pantheonsite.io/manage-service/kafka/">Kafka</a></li>
<li><a href="https://zeppelin.incubator.apache.org/">Zeppelin</a></li>
</ul>

<h1>Quick Start</h1>

<ol>
<li><p>Install DC/OS Spark via the DC/OS CLI:</p>

<p><code>$ dcos package install spark</code></p></li>
<li><p>Run a Spark job:</p>

<p><code>$ dcos spark run --submit-args="--class
org.apache.spark.examples.SparkPi
http://downloads.mesosphere.com.s3.amazonaws.com/assets/spark/spark
-examples_2.10-1.4.0-SNAPSHOT.jar 30"</code></p></li>
<li><p>View your job:</p>

<p>Visit the Spark cluster dispatcher at <code>http://&lt;dcos-url&gt;/service/spark/</code> to view the status of your job. Also visit the Mesos UI at <code>http://&lt;dcos-url&gt;/mesos/</code> to see job logs.</p></li>
</ol>

<h1>Install</h1>

<p>To start a basic Spark cluster, run the following command on the DC/OS CLI. This command installs the dispatcher, and, optionally, the history server. See <a href="#custom">Custom Installation</a> to install the history server.</p>

<p><code>$ dcos package install spark</code></p>

<p>Monitor the deployment at <code>http://&lt;dcos-url&gt;/marathon</code>. Once it is complete, visit Spark at <code>http://&lt;dcos-url&gt;/service/spark/</code>.</p>

<p><a name="custom"></a></p>

<h2>Custom Installation</h2>

<p>You can customize the default configuration properties by creating a JSON options file and passing it to <code>dcos package install --options</code>. For example, to install the history server, create a file called <code>options.json</code>:</p>

<p><code>{
  "history-server": {
    "enabled": true
  }
  }}</code></p>

<p>Then, install Spark with your custom configuration:</p>

<p><code>$ dcos package install --options=options.json spark</code></p>

<p>Run the following command to see all configuration options:</p>

<p><code>$ dcos package describe spark --config</code></p>

<h3>Minimal Installation</h3>

<p>For development purposes, you may wish to install Spark on a local DC/OS cluster. For this, you can use <a href="https://github.com/mesosphere/dcos-vagrant">dcos-vagrant</a>.</p>

<ol>
<li><p>Install DC/OS Vagrant:</p>

<p>Install a minimal DC/OS Vagrant according to the instructions <a href="https://github.com/mesosphere/dcos-vagrant">here</a>.</p></li>
<li><p>Install Spark:</p>

<p><code>$ dcos package install spark</code></p></li>
<li><p>Run a simple Job:</p>

<p><code>$ dcos spark run --submit-args="--class
org.apache.spark.examples.SparkPi
http://downloads.mesosphere.com.s3.amazonaws.com/assets/spark/spark-
examples_2.10-1.5.0.jar"</code></p></li>
</ol>

<p>NOTE: A limited resource environment such as DC/OS Vagrant restricts some of the features available in DC/OS Spark. For example, unless you have enough resources to start up a 5-agent cluster, you will not be able to install DC/OS HDFS, and you thus won't be able to enable the history server.</p>

<p>Also, a limited resource environment can restrict how you size your executors, for example with <code>spark.executor.memory</code>.</p>

<p><a name="hdfs"></a></p>

<h3>HDFS</h3>

<p>By default, DC/OS Spark jobs are configured to read from DC/OS HDFS. To submit Spark jobs that read from a different HDFS cluster, customize <code>hdfs.config-url</code> to be a URL that serves <code>hdfs-site.xml</code> and <code>core-site.xml</code>. <a href="http://spark.apache.org/docs/latest/configuration.html#inheriting-">Learn more</a>.</p>

<p>For DC/OS HDFS, these configuration files are served at <code>http://&lt;hdfs.framework-name&gt;.marathon.mesos:&lt;port&gt;/config/</code>, where <code>&lt;hdfs.framework-name&gt;</code> is a configuration variable set in the HDFS package, and <code>&lt;port&gt;</code> is the port of its marathon app.</p>

<h3>HDFS Kerberos</h3>

<p>You can access external (i.e. non-DC/OS) Kerberos-secured HDFS clusters from Spark on Mesos.</p>

<h4>Credentials</h4>

<p>To authenticate to a Kerberos KDC on Spark, Mesos supports keytab files as well as ticket files (TGTs).</p>

<p>Keytabs are valid infinitely, while tickets can expire. Especially for long-running streaming jobs, keytabs are recommended.</p>

<h5>Keytab Authentication</h5>

<p>On Unix machines with Heimdal Kerberos, the following command creates a compatible keytab:</p>

<p><code>$ ktutil -k user.keytab add -p user@REALM -e aes256-cts-hmac-sha1-96
-V 1</code></p>

<p>Submit the job with the keytab:</p>

<p><code>$ dcos spark run --submit-args="--principal user@REALM --keytab
&amp;lt;keytab-file-path&amp;gt;..."</code></p>

<h5>TGT Authentication</h5>

<p>On Unix machines with Heimdal Kerberos, the following command creates a Ticket Granting Ticket (TGT), which is valid for 3 hours:</p>

<p><code>$ kinit -c user.tgt -f -l 3h -V user@REALM</code></p>

<p>Submit the job with the ticket:</p>

<p><code>$ dcos spark run --principal user@REALM --tgt &amp;lt;ticket-file-path&amp;gt;
…</code></p>

<p><strong>Note:</strong> These credentials are security-critical. We highly recommended <a href="#ssl">configuring SSL encryption</a> between the Spark components when accessing Kerberos-secured HDFS clusters.</p>

<h4>HDFS Configuration</h4>

<p>Once you've set up a Kerberos-enabled HDFS cluster, configure Spark to connect to it. See instructions <a href="#hdfs">here</a>.</p>

<p>It is assumed that the HDFS namenodes are configured in the core-site.xml of Hadoop in this way:</p>

<p>~~~ dfs.ha.namenodes.hdfs nn1,nn2</p>

<p>dfs.namenode.rpc-address.hdfs.nn1 server1:9000</p>

<p>dfs.namenode.http-address.hdfs.nn1 server1:50070</p>

<p>dfs.namenode.rpc-address.hdfs.nn2 server2:9000</p>

<p>dfs.namenode.http-address.hdfs.nn2 server2:50070 ~~~</p>

<h4>Installation</h4>

<ol>
<li><p>Base64 encode your <code>krb5.conf</code> file:</p>

<p><code>$ cat krb5.conf | base64 W2xpYmRlZmF1bHRzXQogICAgICA….</code></p></li>
</ol>

<p>This file tells Spark how to connect to your KDC.</p>

<ol>
<li><p>Add the following to your JSON configuration file to enable Kerberos in Spark:</p>

<p><code>{
"security": {
"kerberos": {
"krb5conf": "W2xp..."
}
}      }
}      }    }</code></p></li>
<li><p>Install Spark with your custom configuration, here called <code>options.json</code>:</p>

<p><code>$ dcos package install --options=options.json spark</code></p></li>
</ol>

<h3>History Server</h3>

<p>DC/OS Spark includes the <a href="http://spark.apache.org/docs/latest/monitoring.html#viewing-after-the-">Spark history server</a>. Because the history server requires HDFS, you must explicitly enable it.</p>

<ol>
<li><p>Install HDFS first:</p>

<p><code>$ dcos package install hdfs</code></p>

<p><strong>Note:</strong> HDFS requires 5 private nodes.</p></li>
<li><p>Create a history HDFS directory (default is <code>/history</code>). <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/administration/sshcluster/">SSH into your cluster</a> and run:</p>

<p><code>$ hdfs dfs -mkdir /history</code></p></li>
<li><p>Enable the history server when you install Spark. Create a JSON configuration file. Here we call it <code>options.json</code>:</p>

<p><code>{
"history-server": {
"enabled": true
}
}    }</code></p></li>
<li><p>Install Spark:</p>

<p><code>$ dcos package install spark --options=options.json</code></p></li>
<li><p>Run jobs with the event log enabled:</p>

<p><code>$ dcos spark run --submit-args=`-Dspark.eventLog.enabled=true
-Dspark.eventLog.dir=hdfs://hdfs/history ... --class MySampleClass
http://external.website/mysparkapp.jar`</code></p></li>
<li><p>Visit your job in the dispatcher at <code>http://&lt;dcos_url&gt;/service/spark/Dispatcher/</code>. It will include a link to the history server entry for that job.</p></li>
</ol>

<p><a name="ssl"></a></p>

<h3>SSL</h3>

<p>SSL support in DC/OS Spark encrypts the following channels:</p>

<ul>
<li>From the <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/administration/dcosarchitecture/components">DC/OS admin router</a> to the dispatcher</li>
<li>From the dispatcher to the drivers</li>
<li>From the drivers to their executors</li>
</ul>

<p>There are a number of configuration variables relevant to SSL setup. List them with the following command:</p>

<p><code>$ dcos package describe spark --config</code></p>

<p>There are only two required variables:</p>

<table class="table">
  <tr>
    <th>
      Variable
    </th>
    
    <th>
      Description
    </th>
  </tr>
  
  <tr>
    <td>
      `spark.ssl.enabled`
    </td>
    
    <td>
      Set to true to enable SSL (default: false).
    </td>
  </tr>
  
  <tr>
    <td>
      spark.ssl.keyStoreBase64
    </td>
    
    <td>
      Base64 encoded blob containing a Java keystore.
    </td>
  </tr>
</table>

<p>The Java keystore (and, optionally, truststore) are created using the <a href="http://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html">Java keytool</a>. The keystore must contain one private key and its signed public key. The truststore is optional and might contain a self-signed root-ca certificate that is explicitly trusted by Java.</p>

<p>Both stores must be base64 encoded, e.g. by:</p>

<p><code>$ cat keystore | base64 /u3+7QAAAAIAAAACAAAAAgA...</code></p>

<p><strong>Note:</strong> The base64 string of the keystore will probably be much longer than the snippet above, spanning 50 lines or so.</p>

<p>With this and the password <code>secret</code> for the keystore and the private key, your JSON options file will look like this:</p>

<p><code>{
  "security": {
    "ssl": {
      "enabled": true,
      "keyStoreBase64": "/u3+7QAAAAIAAAACAAAAAgA...”,
      "keyStorePassword": "secret",
      "keyPassword": "secret"
    }
    }  }
    }  }}</code></p>

<p>Install Spark with your custom configuration:</p>

<p><code>$ docs package install --options=options.json spark</code></p>

<p>In addition to the described configuration, make sure to connect the DC/OS cluster only using an SSL connection, i.e. by using an <code>https://&lt;dcos-url&gt;</code>. Use the following command to set your DC/OS URL:</p>

<p><code>$ dcos config set core.dcos_url https://&amp;lt;dcos-url&amp;gt;</code></p>

<h2>Multiple Install</h2>

<p>Installing multiple instances of the DC/OS Spark package provides basic multi-team support. Each dispatcher displays only the jobs submitted to it by a given team, and each team can be assigned different resources.</p>

<p>To install mutiple instances of the DC/OS Spark package, set each <code>service.name</code> to a unique name (e.g.: "spark-dev") in your JSON configuration file during installation:</p>

<p><code>{
  "service": {
     "name": "spark-dev"
  }
  }}</code></p>

<p>To use a specific Spark instance from the DC/OS Spark CLI:</p>

<p><code>$ dcos config set spark.app_id &lt;service.name&gt;</code></p>

<h1>Upgrade</h1>

<ol>
<li>In the Marathon web interface, destroy the Spark instance to be updated.</li>
<li>Verify that you no longer see it in the DC/OS web interface.</li>
<li><p>Reinstall Spark.</p>

<p><code>$ dcos package install spark</code></p></li>
</ol>

<h1>Run a Spark Job</h1>

<ol>
<li><p>Before submitting your job, upload the artifact (e.g., jar file) to a location visible to the cluster (e.g., S3 or HDFS). <a href="http://spark.apache.org/docs/latest/submitting-applications.html">Learn more</a>.</p></li>
<li><p>Run the job</p>

<p><code>$ dcos spark run --submit-args=`--class MySampleClass
http://external.website/mysparkapp.jar 30`</code></p>

<p><code>dcos spark run</code> is a thin wrapper around the standard Spark <code>spark-submit</code> script. You can submit arbitrary pass-through options to this script via the <code>--submit-args</code> options.</p>

<p>The first time you run a job, the CLI must download the Spark distribution to your local machine. This may take a while.</p>

<p>If your job runs successfully, you will get a message with the job’s submission ID:</p>

<p><code>Run job succeeded. Submission id: driver-20160126183319-0001</code></p></li>
<li><p>View the Spark scheduler progress by navigating to the Spark dispatcher at <code>http://&lt;dcos-url&gt;/service/spark/</code></p></li>
<li><p>View the job's logs through the Mesos UI at <code>http://&lt;dcos-url&gt;/mesos/</code></p></li>
</ol>

<h2>Setting Spark properties</h2>

<p>Spark job settings are controlled by configuring <a href="http://spark.apache.org/docs/latest/configuration.html#spark-">Spark properties</a>. You can set Spark properties during submission, or you can create a configuration file.</p>

<h3>Submission</h3>

<p>All properties are submitted through the <code>--submit-args</code> option to <code>dcos spark run</code>. These are ultimately passed to the <a href="http://spark.apache.org/docs/latest/submitting-applications.html"><code>spark-submit</code> script</a>.</p>

<p>Certain common properties have their own special names. You can view these through <code>dcos spark run --help</code>. Here is an example of using <code>--supervise</code>:</p>

<p><code>$ dcos spark run --submit-args="--supervise --class MySampleClass
http://external.website/mysparkapp.jar 30`</code></p>

<p>Or you can set arbitrary properties as java system properties by using <code>-D&lt;prop&gt;=&lt;value&gt;</code>:</p>

<p><code>$ dcos spark run --submit-args="-Dspark.executor.memory=4g --class
MySampleClass http://external.website/mysparkapp.jar 30`</code></p>

<h3>Configuration file</h3>

<p>To set Spark properties with a configuration file, create a <code>spark-defaults.conf</code> file and set the environment variable <code>SPARK_CONF_DIR</code> to the containing directory. <a href="http://spark.apache.org/docs/latest/configuration.html#overriding-">Learn more</a>.</p>

<h1>Uninstall</h1>

<p><code>$ dcos package uninstall --app-id=&amp;lt;app-id&amp;gt; spark</code></p>

<p>The Spark dispatcher persists state in Zookeeper, so to fully uninstall the Spark DC/OS package, you must go to <code>http://&lt;dcos-url&gt;/exhibitor</code>, click on <code>Explorer</code>, and delete the znode corresponding to your instance of Spark. By default this is <code>spark_mesos_Dispatcher</code>.</p>

<h1>Runtime Configuration Change</h1>

<p>You can customize DC/OS Spark in-place when it is up and running.</p>

<ol>
<li><p>View your Marathon dashboard at <code>http://&lt;dcos-url&gt;/marathon</code></p></li>
<li><p>In the list of <code>Applications</code>, click the name of the Spark framework to be updated.</p></li>
<li><p>Within the Spark instance details view, click the <code>Configuration</code> tab, then click the <code>Edit</code> button.</p></li>
<li><p>In the dialog that appears, expand the <code>Environment Variables</code> section and update any field(s) to their desired value(s).</p></li>
<li><p>Click <code>Change and deploy configuration</code> to apply any changes and cleanly reload Spark.</p></li>
</ol>

<h1>Troubleshooting</h1>

<h2>Dispatcher</h2>

<p>The Mesos cluster dispatcher is responsible for queuing, tracking, and supervising drivers. Potential problems may arise if the dispatcher does not receive the resources offers you expect from Mesos, or if driver submission is failing. To debug this class of issue, visit the Mesos UI at <code>http://&lt;dcos-url&gt;/mesos/</code> and navigate to the sandbox for the dispatcher.</p>

<h2>Jobs</h2>

<ul>
<li><p>DC/OS Spark jobs are submitted through the dispatcher, which displays Spark properties and job state. Start here to verify that the job is configured as you expect.</p></li>
<li><p>The dispatcher further provides a link to the job's entry in the history server, which displays the Spark Job UI. This UI shows the for the job. Go here to debug issues with scheduling and performance.</p></li>
<li><p>Jobs themselves log output to their sandbox, which you can access through the Mesos UI. The Spark logs will be sent to <code>stderr</code>, while any output you write in your job will be sent to <code>stdout</code>.</p></li>
</ul>

<h2>CLI</h2>

<p>The Spark CLI is integrated with the dispatcher so that they always use the same version of Spark, and so that certain defaults are honored. To debug issues with their communication, run your jobs with the <code>--verbose</code> flag.</p>

<h2>HDFS Kerberos</h2>

<p>To debug authentication in a Spark job, enable Java security debug output:</p>

<p><code>$ dcos spark run --submit-args="-Dsun.security.krb5.debug=true..."</code></p>

<h1>Limitations</h1>

<ul>
<li><p>DC/OS Spark only supports submitting jars. It does not support Python or R.</p></li>
<li><p>Spark jobs run in Docker containers. The first time you run a Spark job on a node, it might take longer than you expect because of the <code>docker pull</code>.</p></li>
<li><p>Spark shell is not supported. For interactive analytics, we recommend Zeppelin, which supports visualizations and dynamic dependency management.</p></li>
</ul>

<p>fact hadoop-cluster-configuration / properties configuration-directory</p>
