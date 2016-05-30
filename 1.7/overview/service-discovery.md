---
layout: page
post_title: Service Discovery
menu_order: 0
---

<p>There are two levels of service discovery in DC/OS: Mesos-DNS and VIPs. Mesos-DNS provides the namespace to all cluster hosts. VIPs enable clients to have a single configuration value.</p>

<h1>Mesos-DNS</h1>

<p>Every task started by DC/OS is assigned a well known DNS name. You can even enumerate every <a href="/usage/service-discovery/dns-naming/">DNS name</a> in your cluster. For a Marathon service named "testing", you can find where it is running via:</p>

<pre><code>    dig testing.marathon.mesos
</code></pre>

<p>Take a look at the <a href="/usage/service-discovery/Mesos-DNS/">Mesos-DNS documentation</a> for a more in-depth look at how Mesos-DNS is working and what it is doing for you.</p>

<h1>VIP</h1>

<p>You can assign a VIP to one of your services by following these steps. For some more in depth instructions, check out <a href="/usage/service-discovery/">service discovery</a>.</p>

<ol>
<li><p>From the DC/OS web interface, click on the <strong>Services</strong> tab and select <strong>Marathon</strong>.</p>

<ul>
<li>To create a new application, click <strong>Create Application</strong> and select the <strong>Ports and Service Discovery</strong> menu option.</li>
<li>To edit an existing application, select your application and click the <strong>Configuration</strong> tab, then click <strong>Edit</strong>. You can then select the <strong>Ports and Service Discovery</strong> menu option.</li>
</ul>

<p><img src="/assets/image/ui-marathon-ports.gif" alt="Marathon Ports" /></p></li>
<li><p>Enter the Port, Protocol, Name, and VIP address. Remember that a VIP includes the port, like <code>1.1.1.1:5000</code>.</p>

<p><strong>Tip:</strong> Select <strong>JSON Mode</strong> to edit your application directly in JSON.</p>

<p>For more information on port configuration, see the <a href="/usage/service-discovery/">ports documentation</a>.</p></li>
<li><p>From inside the cluster, you will be able to use your VIP directly. You can <a href="/administration/sshcluster/">SSH</a> into the cluster and run this command to see it work:</p>

<pre><code>curl 1.1.1.1:5000
</code></pre></li>
</ol>
