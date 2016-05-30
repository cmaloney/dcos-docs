---
layout: page
post_title: Install Docker on CentOS
menu_order: 0
---

<p>Docker's <a href="https://docs.docker.com/engine/installation/linux/centos/" target="_blank">CentOS-specific installation instructions</a> are always going to be the most up to date for the latest version of Docker. However, the following recommendations and instructions should make it easier to manage the Docker installation over time and mitigate several known issues with various other configurations.</p>

<h1>Recommendations</h1>

<p>In addition to the general <a href="/administration/installing/custom/system-requirements/#docker">Docker requirements and recommendations for DC/OS</a>, the following CentOS-specific recommendations will improve your DC/OS experience.</p>

<ul>
<li><p>Use Docker's yum repository to install Docker on CentOS. The yum repository makes it easy to upgrade and automatically manages dependency installation.</p></li>
<li><p>Prefer the OverlayFS storage driver. OverlayFS avoids known issues with <code>devicemapper</code> in <code>loop-lvm</code> mode and allows containers to use docker-in-docker, if they want.</p></li>
<li><p>Use CentOS 7.2 or greater. OverlayFS support was improved in 7.2 to fix <a href="https://github.com/docker/docker/issues/10294" target="_blank">a bug with XFS</a>.</p></li>
<li><p>Format node storage as XFS. As of CentOS 7.2, "<a href="https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/7.2_Release_Notes/technology-preview-file_systems.html" target="_blank">only XFS is currently supported for use as a lower layer file system</a>".</p></li>
</ul>

<h1>Instructions</h1>

<p>The following instructions demonstrate how to use Docker with OverlayFS on CentOS 7.</p>

<ol>
<li><p>Upgrade CentOS to 7.2:</p>

<p><code>bash
$ sudo yum upgrade --assumeyes --tolerant
$ sudo yum update --assumeyes</code></p></li>
<li><p>Verify that the kernel is at least 3.10:</p>

<p><code>$ uname -r
3.10.0-327.10.1.el7.x86_64</code></p></li>
<li><p>Enable OverlayFS:</p>

<p><code>bash
$ sudo tee /etc/modules-load.d/overlay.conf &lt;&lt;-'EOF'
overlay
EOF</code></p></li>
<li><p>Reboot to reload kernel modules:</p>

<p><code>bash
$ reboot</code></p></li>
<li><p>Verify that OverlayFS is enabled:</p>

<p><code>bash
$ lsmod | grep overlay
overlay</code></p></li>
<li><p>Configure yum to use the Docker yum repo:</p>

<p><code>bash
$ sudo tee /etc/yum.repos.d/docker.repo &lt;&lt;-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF</code></p></li>
<li><p>Configure systemd to run the Docker Daemon with OverlayFS:</p>

<p><code>bash
$ sudo mkdir -p /etc/systemd/system/docker.service.d &amp;&amp; sudo tee /etc/systemd/system/docker.service.d/override.conf &lt;&lt;- EOF
[Service]
ExecStart=
ExecStart=/usr/bin/docker daemon --storage-driver=overlay -H fd://
EOF</code></p></li>
<li><p>Install the Docker engine, daemon, and service:</p>

<p><code>bash
$ sudo yum install --assumeyes --tolerant docker-engine
$ sudo systemctl start docker
$ sudo systemctl enable docker</code></p>

<p>When the process completes, you should see:</p>

<p><code>Complete!
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.</code></p></li>
<li><p>Test that Docker is properly installed:</p>

<p><code>bash
$ sudo docker ps</code></p></li>
</ol>

<p>For more generic Docker requirements, see <a href="/administration/installing/custom/system-requirements/#docker">System Requirements: Docker</a>.</p>
