---
post_title: Download Apache Mesos RC Packages
menu_order: 0
---
<div class="page-header">
  <h1>
    Download Apache Mesos RC Packages
  </h1>
</div>

*   This page contains information about Apache Mesos release candidate (RC) builds. For official releases, see the [releases][1] page. For nightly builds, see the [mesos-nightly][2] page. *

### All Release Candidates

{% for package in site.data.download_rc_versions %} {% if package.name == "mesos" %} {% assign releases = package.releases | sort:"name" | reverse%} {% for rel in releases %}

#### \[Apache Mesos {{ rel.name }}\]\[3\]    <small>released {{ rel.timestamp | date_to_string }} </small>

{% endfor %} {% endif %} {% endfor %}

{% for package in site.data.download_rc_versions %} {% if package.name == "mesos" %} {% assign releases = package.releases | sort:"name" | reverse%} {% for rel in releases %}

<div id="apache-mesos-{{ rel.release_group }}">
</div>

<table class="table table-striped" id="apache-mesos-{{ rel.name }}">
  <thead>
    <tr>
      <th valign="bottom" align="left">
        <span class="h4">Apache Mesos {{ rel.name | upcase }}</span> <small style="font-weight:normal"> <a href="{{ rel.announcement }}" title="Release notes for Apache Mesos {{ rel.name }}">Release notes</a> </small>
      </th>
      
      <th class="text-right">
        <span title="Release date for Apache Mesos {{ rel.name }}" style="font-weight:normal"> {{ rel.timestamp | date_to_string }} </span>
      </th>
    </tr>
  </thead>
  
  <tbody>
    {% for pkg in rel.packages %} <tr>
      <td style="vertical-align:middle">
        Apache Mesos {{rel.name}} for <a href="http://repos.mesosphere.com/{{ pkg.path }}" title="Apache Mesos {{rel.name}} for {{pkg.name}}">{{pkg.name}}</a>
      </td>
      
      <td align="right">
        {% if pkg.sha256 %} {% assign sha = "SHA 256" %} {% assign sha_val = pkg.sha256 %} {% else %} {% assign sha = "SHA" %} {% assign sha_val = pkg.sha %} {% endif %} <button class="btn btn-link"> {{ sha }} </button> <div class="collapse" id="{{ sha_val }}">
          <small style="font-family:monospace"> {{ sha_val }} </small>
        </div>
      </td>
    </tr> {% endfor %}
  </tbody>
</table>

{% endfor %} {% endif %} {% endfor %}

* * *

### The Source

Apache Mesos is an open source project, and its source is available from the [Mesos Downloads ➦][3] page.

### Installation Tips

*   On many systems, you'll need to ensure `libjvm.so` is on the linker path so that Mesos can find it when it starts. If you have only one JVM installed in the default location, one can approach the problem with a [small shell script][4]. If you have a better idea about how to do this, please contact `support@mesosphere.io` and we'll try to implement it.

*   Configure Mesos to talk to Zookeeper by overwriting `/etc/mesos/zk`.

*   Mesos loads many configuration settings via environment variables, which can be set in `/etc/defaults/mesos`, `/etc/defaults/mesos-master` and `/etc/defaults/mesos-slave`.
    
    [3]: #apache-mesos-{{ rel.name }} "Show packages for Apache Mesos {{ rel.name }}"

 [1]: /downloads/mesos/
 [2]: /downloads/mesos-nightly/
 [3]: https://mesos.apache.org/downloads/
 [4]: https://gist.github.com/solidsnack/7569266