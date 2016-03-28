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

You can install DCOS services on your cluster with a single command `dcos package install` after [installing the DCOS Command-Line Interface][9]. DCOS offers the Universe and Multiverse package repositories.

# Universe

DCOS Universe contains all services that have been certified by Mesosphere. For more information on DCOS Universe, see the [GitHub Universe repository][1].

# Multiverse

DCOS Multiverse contains experimental services that are still being tested and are not guaranteed to work properly with DCOS. Multiverse services are not recommended for production clusters. For more information on DCOS Multiverse, see the [GitHub Multiverse repository][10].

For more information on installing services, see [Managing DCOS services][11].

# Next steps

See the [installing a DCOS service][12] tutorial.

 [1]: https://github.com/mesosphere/universe
 [2]: ../manage-service/cassandra/
 [3]: ../manage-service/chronos/
 [4]: ../manage-service/hdfs/
 [5]: ../manage-service/kafka/
 [6]: ../manage-service/kubernetes/
 [7]: ../manage-service/marathon/
 [8]: ../manage-service/spark/
 [9]: https://docs.mesosphere.com/administration/introcli/cli/
 [10]: https://github.com/mesosphere/multiverse
 [11]: ../manage-service/
 [12]: ../administration/admin-tutorials/install-service/