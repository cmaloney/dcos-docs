---
layout: page
post_title: Configuration parameters
menu_order: 100
---
<p>These configuration parameters are specified in <a href="https://en.wikipedia.org/wiki/YAML">YAML</a> format in your config.yaml file. During DC/OS installation the configuration file is used to generate a customized DC/OS build.</p>

<h1>Cluster Setup</h1>

<h3>agent_list</h3>

<p>This parameter specifies a YAML nested list (<code>-</code>) of IPv4 addresses to your agent host names.</p>

<h3>bootstrap_url</h3>

<p>This required parameter specifies the URI path for the DC/OS installer to store the customized DC/OS build files. If you are using the automated DC/OS installer, you should specify <code>bootstrap_url: file:///opt/dcos_install_tmp</code> unless you have moved the installer assets. By default the automated DC/OS installer places the build files in <code>file:///opt/dcos_install_tmp</code>.</p>

<h3>cluster_name</h3>

<p>This parameter specifies the name of your cluster.</p>

<h3>exhibitor_storage_backend</h3>

<p>This parameter specifies the type of storage backend to use for Exhibitor. You can use internal DC/OS storage (<code>static</code>) or specify an external storage system (<code>zookeeper</code>, <code>aws_s3</code>, and <code>shared_filesystem</code>) for configuring and orchestrating Zookeeper with Exhibitor on the master nodes. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DC/OS installation.</p>

<ul>
<li><code>exhibitor_storage_backend: static</code> This option specifies that the Exhibitor storage backend is managed internally within your cluster. This is the default value.</li>
<li><p><code>exhibitor_storage_backend: zookeeper</code> This option specifies a ZooKeeper instance for shared storage. If you use a ZooKeeper instance to bootstrap Exhibitor, this ZooKeeper instance must be separate from your DC/OS cluster. You must have at least 3 ZooKeeper instances running at all times for high availability. If you specify <code>zookeeper</code>, you must also specify these parameters.</p>

<ul>
<li><strong>exhibitor_zk_hosts</strong> This parameter specifies a comma-separated list of one or more ZooKeeper node IP addresses to use for configuring the internal Exhibitor instances. Exhibitor uses this ZooKeeper cluster to orchestrate it's configuration. Multiple ZooKeeper instances are recommended for failover in production environments.</li>
<li><strong>exhibitor_zk_path</strong> This parameter specifies the filepath that Exhibitor uses to store data, including the <code>zoo.cfg</code> file.</li>
</ul></li>
<li><p><code>exhibitor_storage_backend: aws_s3</code> This option specifies an Amazon Simple Storage Service (S3) bucket for shared storage. If you specify <code>aws_s3</code>, you must also specify these parameters.</p>

<ul>
<li><strong>aws_access_key_id</strong> This parameter specifies AWS key ID.</li>
<li><strong>aws_region</strong> This parameter specifies AWS region for your S3 bucket.</li>
<li><strong>aws_secret_access_key</strong> This parameter specifies AWS secret access key.</li>
<li><strong>exhibitor_explicit_keys</strong> This parameter specifies whether you are using AWS API keys to grant Exhibitor access to S3. 

<ul>
<li><code>exhibitor_explicit_keys: 'true'</code> If you're using AWS API keys to manually grant Exhibitor access.</li>
<li><code>exhibitor_explicit_keys: 'false'</code> If you're using AWS Identity and Access Management (IAM) to grant Exhibitor access to s3.</li>
</ul></li>
<li><strong>s3_bucket</strong> This parameter specifies name of your S3 bucket.</li>
<li><strong>s3_prefix</strong> This parameter specifies S3 prefix to be used within your S3 bucket to be used by Exhibitor.</li>
</ul>

<p><strong>Tip:</strong> AWS EC2 Classic is not supported.</p></li>
<li><p><code>exhibitor_storage_backend: shared_filesystem</code> This option specifies a Network File System (NFS) mount for shared storage. If you specify <code>shared_filesystem</code>, you must also specify this parameter:</p>

<ul>
<li><p><strong>exhibitor_fs_config_dir</strong> This parameter specifies the absolute path to the folder that Exhibitor uses to coordinate its configuration. This should be a directory inside of a Network File System (NFS) mount. For example, if every master has <code>/fserv</code> mounted via NFS, set as <code>exhibitor_fs_config_dir: /fserv/dcos-exhibitor</code>.</p>

<p><strong>Important:</strong> With <code>shared_filesystem</code>, all masters must must have the NFS volume mounted and <code>exhibitor_fs_config_dir</code> must be inside of it. If any of your servers are missing the mount, the DC/OS cluster will not start.</p></li>
</ul></li>
</ul>

<p>This parameter specifies the type of storage backend to use for Exhibitor. You can use internal DC/OS storage (<code>static</code>) or specify an external storage system (<code>zookeeper</code>, <code>aws_s3</code>, and <code>shared_filesystem</code>) for configuring and orchestrating Zookeeper with Exhibitor on the master nodes. Exhibitor automatically configures your Zookeeper installation on the master nodes during your DC/OS installation.</p>

<ul>
<li><code>exhibitor_storage_backend: static</code> This option specifies that the Exhibitor storage backend is managed internally within your cluster.</li>
<li><p><code>exhibitor_storage_backend: zookeeper</code> This option specifies a ZooKeeper instance for shared storage. If you use a ZooKeeper instance to bootstrap Exhibitor, this ZooKeeper instance must be separate from your DC/OS cluster. You must have at least 3 ZooKeeper instances running at all times for high availability. If you specify <code>zookeeper</code>, you must also specify these parameters.</p>

<ul>
<li><strong>exhibitor_zk_hosts</strong> This parameter specifies a comma-separated list of one or more ZooKeeper node IP addresses to use for configuring the internal Exhibitor instances. Exhibitor uses this ZooKeeper cluster to orchestrate it's configuration. Multiple ZooKeeper instances are recommended for failover in production environments.</li>
<li><strong>exhibitor_zk_path</strong> This parameter specifies the filepath that Exhibitor uses to store data, including the <code>zoo.cfg</code> file.</li>
</ul></li>
<li><p><code>exhibitor_storage_backend: aws_s3</code> This option specifies an Amazon Simple Storage Service (S3) bucket for shared storage. If you specify <code>aws_s3</code>, you must also specify these parameters:</p>

<ul>
<li><strong>aws_access_key_id</strong> This parameter specifies AWS key ID.</li>
<li><strong>aws_region</strong> This parameter specifies AWS region for your S3 bucket.</li>
<li><strong>aws_secret_access_key</strong> This parameter specifies AWS secret access key.</li>
<li><strong>exhibitor_explicit_keys</strong> This parameter specifies whether you are using AWS API keys to grant Exhibitor access to S3. 

<ul>
<li><code>exhibitor_explicit_keys: 'true'</code> If you're using AWS API keys to manually grant Exhibitor access.</li>
<li><code>exhibitor_explicit_keys: 'false'</code> If you're using AWS Identity and Access Management (IAM) to grant Exhibitor access to s3.</li>
</ul></li>
<li><strong>s3_bucket</strong> This parameter specifies name of your S3 bucket.</li>
<li><strong>s3_prefix</strong> This parameter specifies S3 prefix to be used within your S3 bucket to be used by Exhibitor.</li>
</ul></li>
<li><p><code>exhibitor_storage_backend: shared_filesystem</code> This option specifies a Network File System (NFS) mount for shared storage. If you specify <code>shared_filesystem</code>, you must also specify this parameter:</p>

<ul>
<li><p><strong>exhibitor_fs_config_dir</strong> This parameter specifies the absolute path to the folder that Exhibitor uses to coordinate its configuration. This should be a directory inside of a Network File System (NFS) mount. For example, if every master has <code>/fserv</code> mounted via NFS, set as <code>exhibitor_fs_config_dir: /fserv/dcos-exhibitor</code>.</p>

<p><strong>Important:</strong> With <code>shared_filesystem</code>, all masters must must have the NFS volume mounted and <code>exhibitor_fs_config_dir</code> must be inside of it. If any of your servers are missing the mount, the DC/OS cluster will not start.</p></li>
</ul></li>
</ul>

<h3><a name="master"></a>master_discovery</h3>

<p>This required parameter specifies the Mesos master discovery method. The available options are <code>static</code> or <code>vrrp</code>.</p>

<ul>
<li><p><code>master_discovery: static</code> This option specifies that Mesos agents are used to discover the masters by giving each agent a static list of master IPs. The masters must not change IP addresses, and if a master is replaced, the new master must take the old master's IP address. If you specify <code>static</code>, you must also specify this parameter:</p>

<ul>
<li><strong>master_list</strong> This required parameter specifies a list of your static master IP addresses as a YAML nested series (<code>-</code>).</li>
</ul></li>
<li><p><code>master_discovery: vrrp</code> This option specifies that Keepalived with a VIP is used to discover the master. You are required to maintain this VIP infrastructure. If you specify <code>vrrp</code>, you must also specify these parameters:</p>

<ul>
<li><strong>keepalived_router_id</strong> This parameter specifies the virtual router ID of the Keepalived cluster. You must use the same virtual router ID across your cluster.</li>
<li><strong>keepalived_interface</strong> This parameter specifies the interface that Keepalived uses.</li>
<li><strong>keepalived_pass</strong> If you've set your <code>auth_type</code> to <code>PASS</code>, this parameter specifies the password that you set for <code>auth_pass</code> in your Keepalived configuration file.</li>
<li><strong>keepalived_virtual_ipaddress</strong> This parameter specifies the VIP in use by your Keepalived cluster. </li>
<li><strong>num_masters</strong> This parameter specifies the number of Mesos masters in your DC/OS cluster. If <code>master_discovery: static</code>, do not use the <code>num_masters</code> parameter. </li>
</ul></li>
</ul>

<h3><a name="rexray-config"></a>rexray_config_method</h3>

<p>This parameter specifies the <a href="https://rexray.readthedocs.org/en/v0.3.2/user-guide/config/" target="_blank">REX-Ray</a> configuration method for enabling external persistent volumes in Marathon. REX-Ray is a storage orchestration engine. For more information, see the external persistent volumes <a href="/usage/services/marathon/external-volumes/">documentation</a>.</p>

<ul>
<li><code>rexray_config_method: empty</code> An empty REX-ray configuration. This is the default value.</li>
<li><code>aws</code> A REX-Ray configuration that is set up for AWS EC2 (EBS) and AWS Identity and Access Management (IAM).</li>
<li><p><code>rexray_config_method: file</code> Specify the path to a REX-Ray configuration file with <code>rexray_config_filename</code>.</p>

<ul>
<li><p><code>rexray_config_filename</code> The path of a REX-Ray configuration file. For example:</p>

<p>rexray_config_filename: genconf/rexray.yaml</p></li>
</ul></li>
</ul>

<h2>Security and Authentication</h2>

<h3>auth_cookie_secure_flag</h3>

<p>This parameter specifies whether to allow web browsers to send the DC/OS authentication cookie through a non-HTTPS connection.</p>

<ul>
<li><code>auth_cookie_secure_flag: false</code> Send the DC/OS authentication cookie through non-HTTPS connections. If you are accessing the DC/OS cluster through an HTTP connection, this is the required setting. This is the default value.</li>
<li><code>auth_cookie_secure_flag: true</code> Require an HTTPS connection to send the DC/OS authentication cookie. If you are accessing the DC/OS cluster through only HTTPS connections, this is the recommended setting.</li>
</ul>

<h3>customer_key</h3>

<p>This parameter specifies the Enterprise DC/OS customer ID. The customer ID is obtained by contacting <a href="mailto:&#x73;&#097;&#108;&#101;&#115;&#064;&#109;&#101;&#115;&#111;&#115;p&#x68;&#x65;&#x72;&#x65;&#x2e;&#x69;&#x6f;">&#x73;&#097;&#108;&#101;&#115;&#064;&#109;&#101;&#115;&#111;&#115;p&#x68;&#x65;&#x72;&#x65;&#x2e;&#x69;&#x6f;</a>. <!-- Needs work --></p>

<h3>ssh_key_path</h3>

<p>This parameter specifies the path to the installer uses to log into the target nodes. By default this is set to <code>/genconf/ssh_key</code>. This parameter should not be changed because <code>/genconf</code> is local to the container that is running the installer, and is a mounted volume.</p>

<h3>ssh_port</h3>

<p>This parameter specifies the port to SSH to, for example <code>22</code>.</p>

<h3>ssh_user</h3>

<p>This parameter specifies the SSH username, for example <code>centos</code>.</p>

<h3>superuser_password_hash</h3>

<p>This required parameter specifies the hashed superuser password. The <code>superuser_password_hash</code> is generated by using the installer <code>--hash-password</code> flag. For more information, see <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/administration/security/managing-authorization/" target="_blank">Managing Authorization and Authentication</a>.</p>

<h3>superuser_username</h3>

<p>This required parameter specifies the Admin username. For more information, see <a href="https://gitsync-mesosphere-documentation.pantheonsite.io/administration/security/managing-authorization/" target="_blank">Managing Authorization and Authentication</a>.</p>

<h3>telemetry_enabled</h3>

<p>This parameter specifies whether to enable sharing of anonymous data for your cluster.</p>

<ul>
<li><code>telemetry_enabled: 'true'</code> Enable anonymous data sharing. This is the default value.</li>
<li><code>telemetry_enabled: 'false'</code> Disable anonymous data sharing.</li>
</ul>

<p>If you’ve already installed your cluster and would like to disable this in-place, you can go through an upgrade <!-- link? --> with the same parameter set.</p>

<h2>Networking</h2>

<h3><a name="dns-search"></a>dns_search</h3>

<p>This parameter specifies a space-separated list of domains that are tried when an unqualified domain is entered (e.g. domain searches that do not contain ‘.’). The Linux implementation of <code>/etc/resolv.conf</code> restricts the maximum number of domains to 6 and the maximum number of characters the setting can have to 256. For more information, see <a href="http://man7.org/linux/man-pages/man5/resolv.conf.5.html">man /etc/resolv.conf</a>.</p>

<p>A <code>search</code> line with the specified contents is added to the <code>/etc/resolv.conf</code> file of every cluster host. <code>search</code> can do the same things as <code>domain</code> and is more extensible because multiple domains can be specified.</p>

<p>In this example, <code>example.com</code> has public website <code>www.example.com</code> and all of the hosts in the datacenter have fully qualified domain names that end with <code>dc1.example.com</code>. One of the hosts in your datacenter has the hostname <code>foo.dc1.example.com</code>. If <code>dns_search</code> is set to ‘dc1.example.com example.com’, then every DC/OS host which does a name lookup of foo will get the A record for <code>foo.dc1.example.com</code>. If a machine looks up <code>www</code>, first <code>www.dc1.example.com</code> would be checked, but it does not exist, so the search would try the next domain, lookup <code>www.example.com</code>, find an A record, and then return it.</p>

<pre><code>dns_search: dc1.example.com dc1.example.com example.com dc1.example.com dc2.example.com example.com
</code></pre>

<h3>resolvers</h3>

<p>This required parameter specifies a YAML nested list (<code>-</code>) of DNS resolvers for your DC/OS cluster nodes. You can specify a maximum of 3 resolvers. Set this parameter to the most authoritative nameservers that you have. If you want to resolve internal hostnames, set it to a nameserver that can resolve them. If you have no internal hostnames to resolve, you can set this to a public nameserver like Google or AWS. In the example file above, the <a href="https://developers.google.com/speed/public-dns/docs/using" target="_blank">Google Public DNS IP addresses (IPv4)</a> are specified (<code>8.8.8.8</code> and <code>8.8.4.4</code>).</p>

<p><strong>Caution:</strong> If you set the <code>resolvers</code> parameter incorrectly, you will permanently damage your configuration and have to reinstall DC/OS.</p>

<h2>Performance and Tuning</h2>

<h3><a name="docker-remove"></a>docker_remove_delay</h3>

<p>This parameter specifies the amount of time to wait before removing stale Docker images stored on the agent nodes and the Docker image generated by the installer. It is recommended that you accept the default value 1 hour.</p>

<h3><a name="gc-delay"></a>gc_delay</h3>

<p>This parameter specifies the maximum amount of time to wait before cleaning up the executor directories. It is recommended that you accept the default value of 2 days.</p>

<h3><a name="log_directory"></a>log_directory</h3>

<p>This parameter specifies the path to the installer host logs from the SSH processes. By default this is set to <code>/genconf/logs</code>. In most cases this should not be changed because <code>/genconf</code> is local to the container that is running the installer, and is a mounted volume.</p>

<h3><a name="process_timeout"></a>process_timeout</h3>

<p>This parameter specifies the allowable amount of time, in seconds, for an action to begin after the process forks. This parameter is not the complete process time. The default value is 120 seconds.</p>

<p><strong>Tip:</strong> If have a slower network environment, consider changing to <code>process_timeout: 600</code>. &lt;!-- ### <a name="roles"></a>roles This parameter specifies the Mesos roles to delegate to a node. For more information, see <a href="https://open.mesosphere.com/reference/mesos-master/#roles" target="_blank">Mesos roles</a>. The available options are `slave_public`, ` master `, and `slave`. * `roles: slave_public` Runs the public agent node. This is the default value. * `roles: master` Runs the master node. * `roles: slave` Runs the private agent node. --&gt; &lt;!-- ### 

<a name="weights"></a>weights This parameter specifies the priority of the role. For more information, see <a href="https://open.mesosphere.com/reference/mesos-master/#weights" target="_blank">Mesos weights</a>. --&gt;</p>

<h1><a name="examples1"></a>Example Configurations</h1>

<h4>DC/OS cluster with 3 masters, 5 agents, static master list specified.</h4>

<pre><code>agent_list:
- &lt;agent-private-ip-1&gt;
- &lt;agent-private-ip-2&gt;
- &lt;agent-private-ip-3&gt;
- &lt;agent-private-ip-4&gt;
- &lt;agent-private-ip-5&gt;
bootstrap_url: 'file:///opt/dcos_install_tmp'
cluster_name: '&lt;cluster-name&gt;'
log_directory: /genconf/logs
master_discovery: static 
master_list:
- &lt;master-private-ip-1&gt;
- &lt;master-private-ip-2&gt;
- &lt;master-private-ip-3&gt;
process_timeout: 120
resolvers:
- &lt;dns-resolver-1&gt;
- &lt;dns-resolver-2&gt;
ssh_key_path: /genconf/ssh-key
ssh_port: '&lt;port-number&gt;'
ssh_user: &lt;username&gt;
</code></pre>

<h4><a name="shared"></a>DC/OS cluster with 3 masters, an Exhibitor/Zookeeper shared filesystem storage backend, Internal DNS</h4>

<pre><code>agent_list:
- &lt;agent-private-ip-1&gt;
- &lt;agent-private-ip-2&gt;
- &lt;agent-private-ip-3&gt;
- &lt;agent-private-ip-4&gt;
- &lt;agent-private-ip-5&gt;
bootstrap_url: file:///tmp/dcos
cluster_name: fs-example
exhibitor_fs_config_dir: /shared-mount
exhibitor_storage_backend: shared_filesystem
log_directory: /genconf/logs
master_discovery: static
master_list:
- &lt;master-private-ip-1&gt;
- &lt;master-private-ip-2&gt;
- &lt;master-private-ip-3&gt;
process_timeout: 120
resolvers:
- 10.10.5.1
- 10.10.6.1
roles: slave_public
ssh_key_path: /genconf/ssh-key
ssh_port: '&lt;port-number&gt;'
ssh_user: &lt;username&gt;
weights: slave_public=1
</code></pre>

<h4><a name="aws"></a>DC/OS Cluster with 3 masters, an Exhibitor/Zookeeper backed by an AWS S3 bucket, AWS DNS, and a public agent node</h4>

<pre><code>agent_list:
- &lt;agent-private-ip-1&gt;
- &lt;agent-private-ip-2&gt;
- &lt;agent-private-ip-3&gt;
- &lt;agent-private-ip-4&gt;
- &lt;agent-private-ip-5&gt;
aws_access_key_id: AKIAIOSFODNN7EXAMPLE
aws_region: us-west-2
aws_secret_access_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
bootstrap_url: file:///tmp/dcos
cluster_name: s3-example
exhibitor_storage_backend: aws_s3
exhibitor_explicit_keys: 'true'
log_directory: /genconf/logs
master_discovery: static
master_list:
- &lt;master-private-ip-1&gt;
- &lt;master-private-ip-2&gt;
- &lt;master-private-ip-3&gt;
process_timeout: 120
resolvers: 
- 169.254.169.253
roles: slave_public
s3_bucket: mybucket
s3_prefix: s3-example
ssh_key_path: /genconf/ssh-key
ssh_port: '&lt;port-number&gt;'
ssh_user: &lt;username&gt;
weights: slave_public=1
</code></pre>

<h4><a name="zk"></a>DC/OS cluster with 3 masters, an Exhibitor/Zookeeper backed by Zookeeper, VRRP master discovery, public agent node, and Google DNS</h4>

<pre><code>agent_list:
- &lt;agent-private-ip-1&gt;
- &lt;agent-private-ip-2&gt;
- &lt;agent-private-ip-3&gt;
- &lt;agent-private-ip-4&gt;
- &lt;agent-private-ip-5&gt;
bootstrap_url: file:///tmp/dcos
cluster_name: zk-example
exhibitor_storage_backend: zookeeper
exhibitor_zk_hosts: 10.10.10.1:2181
exhibitor_zk_path: /zk-example
keepalived_interface: eth1
keepalived_pass: $MY_STRONG_PASSWORD
keepalived_router_id: 51
keepalived_virtual_ipaddress: 67.34.242.55
log_directory: /genconf/logs
master_discovery: vrrp
num_masters: 3
process_timeout: 120
resolvers: 
- 8.8.4.4
- 8.8.8.8
roles: slave_public
ssh_key_path: /genconf/ssh-key
ssh_port: '&lt;port-number&gt;'
ssh_user: &lt;username&gt;
weights: slave_public=1
</code></pre>
