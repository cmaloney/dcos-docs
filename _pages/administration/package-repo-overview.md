---
UID: 56f98449c9e25
post_title: Package Repository
post_excerpt: ""
layout: page
published: true
menu_order: 3
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
DCOS services are Mesosphere-certified applications that are packaged and available from the public DCOS package repositories that are hosted on GitHub. Available DCOS services include Mesosphere-certified [Mesos frameworks][1] and other applications. A Mesos framework is the combination of a Mesos scheduler and an optional custom executor.

DCOS services can be created by the community and by Mesosphere. The Mesosphere-certified services are [Cassandra][2], [Chronos][3], [HDFS][4], [Kafka][5], [Kubernetes][6], [Marathon][7], and [Spark][8].

You can install DCOS services on your cluster with a single command `dcos package install` after [installing the DCOS Command-Line Interface][9]. 

# Universe

DCOS Universe contains all services that have been certified by Mesosphere. For more information on DCOS Universe, see the [GitHub Universe repository][1].

For more information on installing services, see [Managing DCOS services][11].

# Next steps

See the [installing a DCOS service][12] tutorial.

 [1]: https://github.com/mesosphere/universe
 [2]: /usage/managing-services/cassandra/
 [3]: /usage/managing-services/chronos/
 [4]: /usage/managing-services/hdfs/
 [5]: /usage/managing-services/kafka/
 [6]: /usage/managing-services/kubernetes/
 [7]: /usage/managing-services/marathon/
 [8]: /usage/managing-services/spark/
 [9]: /administration/cli/install-cli/
 [10]: https://github.com/mesosphere/multiverse
 [11]: /usage/managing-services/
 [12]: /administration/admin-tutorials/install-service/