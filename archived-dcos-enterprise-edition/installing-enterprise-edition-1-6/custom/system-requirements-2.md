---
layout: page
post_title: System Requirements
menu_order: 0
---
<h1>Hardware Prerequisites</h1>

<p>You must have a single bootstrap node, Mesos master nodes, and Mesos agent nodes.</p>

<h2>Bootstrap node</h2>

<p>1 node with 2 Cores, 16 GB RAM, 60 GB HDD. This is the node where DC/OS installation is run. This bootstrap node must also have:</p>

<ul>
<li>Python, pip, and virtualenv must be installed for the DC/OS <a href="/usage/cli/">CLI</a>. pip must be configured to pull packages from PyPI or your private PyPI, if applicable.</li>
<li>A High-availability (HA) load balancer, such as HAProxy to balance the following TCP ports to all master nodes: 80, 443, 8080, 8181, 2181, 5050.</li>
</ul>

<h2>Cluster nodes</h2>

<p>The cluster nodes are designated Mesos masters and agents during installation.</p>

<h3>Master nodes</h3>

<p>Here are the master node hardware requirements.</p>

<table class="table">
  <tr>
    <th>
      Minimum
    </th>
    
    <th>
      Recommended
    </th>
  </tr>
  
  <tr>
    <td>
      Nodes: 1<br />OS: Enterprise Linux 7 kernel 3.10.0-327 or CoreOS Stable<br />Processor: 4 cores<br />Memory: 32 GB RAM<br />Hard disk space: 120 GB
    </td>
    
    <td>
      Nodes: 3<br />OS: Enterprise Linux 7 kernel 3.10.0-327 or CoreOS Stable<br />Processor: 4 cores<br />Memory: 32 GB RAM<br />Hard disk space: 120 GB
    </td>
  </tr>
  
  <tr>
    <td colspan="2">
      <p>
        There are many mixed workloads on the masters, for example Mesos replicated log and Zookeeper. Some of these require fsync()ing every so often, and this can generate a lot of very expensive random I/O. We recommend the following: <ul>
          <li>
            Solid-state drive (SSD)
          </li>
          <li>
            RAID controllers with a BBU
          </li>
          <li>
            RAID controller cache configured in writeback mode
          </li>
        </ul>
      </p>
    </td>
  </tr>
</table>

<h3>Agent nodes</h3>

<p>Here are the agent node hardware requirements.</p>

<table class="table">
  <tr>
    <th class="tg-e3zv">
      Minimum
    </th>
    
    <th class="tg-e3zv">
      Recommended
    </th>
  </tr>
  
  <tr>
    <td class="tg-031e">
      Nodes: 1<br />OS: Enterprise Linux 7 kernel 3.10.0-327 or CoreOS Stable<br />Processor: 2 cores<br />Memory: 16 GB RAM<br />Hard disk space: 60 GB
    </td>
    
    <td class="tg-031e">
      Nodes: 6<br />OS: Enterprise Linux 7 kernel 3.10.0-327 or CoreOS Stable<br />Processor: 2 cores<br />Memory: 16 GB RAM<br />Hard disk space: 60 GB
    </td>
  </tr>
  
  <tr>
    <td colspan="2">
      The agent nodes must also have a <code>/var</code> directory with 10 GB or more of free space. This directory is used by the sandbox for both Docker and Mesos Containerizer.* Network Access to a public Docker repository or to an internal Docker registry.</ul>
    </td>
  </tr>
</table>

<p></ul></p>

<ul>
<li><p>Your Linux distribution must be running the latest version. You can update CentOS with this command:</p>

<pre><code>$ sudo yum upgrade -y
</code></pre></li>
<li><p>On RHEL 7 and CentOS 7, firewalld must be stopped and disabled. It is a known <a href="https://github.com/docker/docker/issues/16137" target="_blank">Docker issue</a> that firewalld interacts poorly with Docker. For more information, see the <a href="https://docs.docker.com/v1.6/installation/centos/#firewalld" target="_blank">Docker CentOS firewalld</a> documentation.</p>

<pre><code>$ sudo systemctl stop firewalld &amp;&amp; sudo systemctl disable firewalld
</code></pre></li>
</ul>

<h3>Port Configuration</h3>

<ul>
<li>Each node has IP-to-IP connectivity from itself to all nodes in the DC/OS cluster.</li>
<li>Each node has Network Time Protocol (NTP) for clock synchronization enabled.</li>
<li>Each node has ICMP enabled.</li>
<li>Each node has TCP and UDP enabled port 53 for DNS.</li>
<li>All hostnames (FQDN and short hostnames) must be resolvable in DNS, both forward and reverse lookups must succeed. </li>
<li><p>These ports must be open for communication from the master nodes to the agent nodes:</p>

<table class="table">
  <tr>
    <th class="tg-e3zv">
      TCP Port
    </th>

    <th class="tg-e3zv">
      Description
    </th>
  </tr>

  <tr>
    <td class="tg-yw4l">
      5051
    </td>

    <td class="tg-yw4l">
      Mesos agent nodes
    </td>
  </tr>
</table></li>
<li><p>These ports must be open for communication from the agent nodes to the master nodes.</p>

<table class="table">
  <tr>
    <th class="tg-e3zv">
      TCP Port
    </th>

    <th class="tg-e3zv">
      Description
    </th>
  </tr>

  <tr>
    <td class="tg-yw4l">
      2181
    </td>

    <td class="tg-yw4l">
      ZooKeeper, see the <a href="http://zookeeper.apache.org/doc/r3.1.2/zookeeperAdmin.html#sc_zkCommands" target="_blank">ZK Admin Guide</a>
    </td>
  </tr>

  <tr>
    <td class="tg-yw4l">
      2888
    </td>

    <td class="tg-yw4l">
      Exhibitor, see the <a href="https://github.com/Netflix/exhibitor/wiki/REST-Introduction" target="_blank">Exhibitor REST Documentation</a>
    </td>
  </tr>

  <tr>
    <td class="tg-yw4l">
      3888
    </td>

    <td class="tg-yw4l">
      Exhibitor, see the <a href="https://github.com/Netflix/exhibitor/wiki/REST-Introduction" target="_blank">Exhibitor REST Documentation</a>
    </td>
  </tr>

  <tr>
    <td class="tg-031e">
      5050
    </td>

    <td class="tg-031e">
      Mesos master nodes
    </td>
  </tr>

  <tr>
    <td class="tg-031e">
      5051
    </td>

    <td class="tg-031e">
      Mesos agent nodes
    </td>
  </tr>

  <tr>
    <td class="tg-031e">
      8080
    </td>

    <td class="tg-031e">
      Marathon
    </td>
  </tr>

  <tr>
    <td class="tg-031e">
      8123
    </td>

    <td class="tg-031e">
      Mesos-DNS API
    </td>
  </tr>

  <tr>
    <td class="tg-yw4l">
      8181
    </td>

    <td class="tg-yw4l">
      Exhibitor, see the <a href="https://github.com/Netflix/exhibitor/wiki/REST-Introduction" target="_blank">Exhibitor REST Documentation</a>
    </td>
  </tr>
</table></li>
</ul>

<h1>Software Prerequisites</h1>

<h2>All Nodes</h2>

<h3><a name="docker"></a>Docker</h3>

<p><strong>Requirements</strong></p>

<ul>
<li>Docker 1.7 or greater must be installed on all bootstrap and cluster nodes.</li>
</ul>

<p><strong>Recommendations</strong></p>

<ul>
<li><p>Docker 1.9 or greater is recommended <a href="https://github.com/docker/docker/issues/9718" target="_blank">for stability reasons</a>.</p></li>
<li><p>Do not use Docker <code>devicemapper</code> storage driver in <code>loop-lvm</code> mode. For more information, see <a href="https://docs.docker.com/engine/userguide/storagedriver/device-mapper-driver/">Docker and the Device Mapper storage driver</a>.</p></li>
<li><p>Prefer <code>OverlayFS</code> or <code>devicemapper</code> in <code>direct-lvm</code> mode when choosing a production storage driver. For more information, see Docker's <a href="https://docs.docker.com/engine/userguide/storagedriver/selectadriver/" target="_blank">Select a Storage Driver</a>.</p></li>
<li><p>Manage Docker on CentOS with systemd. systemd handles starting Docker on boot and restarting it when it crashes.</p></li>
<li><p>Run Docker commands as the root user (with <code>sudo</code>) or as a user in the <a href="https://docs.docker.com/engine/installation/linux/centos/#create-a-docker-group" target="_blank">docker user group</a>.</p></li>
</ul>

<p><strong>Distribution-Specific Installation</strong></p>

<p>Each Linux distribution requires Docker to be installed in a specific way:</p>

<ul>
<li><strong>CoreOS</strong> - Comes with Docker pre-installed and pre-configured.</li>
<li><p><strong>RHEL</strong> - Install Docker by using a subscription channel. For more information, see <a href="https://access.redhat.com/articles/881893" target="_blank">Docker Formatted Container Images on Red Hat Systems</a>. <!-- $ curl -sSL https://get.docker.com | sudo sh --></p></li>
<li><p><strong>CentOS</strong> - <a href="/administration/installing/custom/install-docker-centos/">Install Docker from Docker's yum repository</a>.</p></li>
</ul>

<p>For more more information, see Docker's <a href="http://docs.docker.com/engine/installation/" target="_blank">distribution-specific installation instructions</a>.</p>

<h2>Bootstrap node</h2>

<p>Before installing DC/OS, you must ensure that your bootstrap node has the following prerequisites.</p>

<h3>DC/OS setup file</h3>

<p>Download and save the DC/OS setup file to your bootstrap node. This file is used to create your customized DC/OS build file. Contact your sales representative or <a href="mailto:&#x73;&#097;&#108;&#x65;&#115;&#064;&#x6d;&#101;&#x73;&#x6f;&#115;&#x70;&#x68;&#101;&#x72;e&#046;&#x63;&#111;&#109;">&#x73;&#097;&#108;&#x65;&#115;&#064;&#x6d;&#101;&#x73;&#x6f;&#115;&#x70;&#x68;&#101;&#x72;e&#046;&#x63;&#111;&#109;</a> to obtain the DC/OS setup file.</p>

<h3>Disable SELinux</h3>

<pre><code>$ setenforce 0
</code></pre>

<h3>Docker Nginx (advanced installer)</h3>

<p>For advanced install only, install the Docker Nginx image with this command:</p>

<pre><code>$ sudo docker pull nginx
</code></pre>

<h2>Cluster nodes</h2>

<p>For advanced install only, your cluster nodes must have the following prerequisites. The cluster nodes are designated as Mesos masters and agents during installation.</p>

<h3>Data compression (advanced installer)</h3>

<p>You must have the <a href="http://www.info-zip.org/UnZip.html" target="_blank">UnZip</a>, <a href="https://www.gnu.org/software/tar/" target="_blank">GNU tar</a>, and <a href="http://tukaani.org/xz/" target="_blank">XZ Utils</a> data compression utilities installed on your cluster nodes.</p>

<p>To install these utilities on CentOS7 and RHEL7:</p>

<pre><code>$ sudo yum install -y tar xz unzip curl ipset
</code></pre>

<h3>Cluster permissions (advanced installer)</h3>

<p>On each of your cluster nodes, use the following command to:</p>

<ul>
<li>Disable SELinux or set it to permissive mode.</li>
<li>Add nogroup to each of your Mesos masters and agents.</li> </li>
<li><p>Reboot your cluster for the changes to take affect</p></p>

<pre><code>$ sudo sed -i s/SELINUX=enforcing/SELINUX=permissive/g /etc/selinux/config &amp;&amp;
 sudo groupadd nogroup &amp;&amp;
 sudo reboot
</code></pre>

<p><strong>Tip:</strong> It may take a few minutes for your node to come back online after reboot.</p></li>
</ul>
