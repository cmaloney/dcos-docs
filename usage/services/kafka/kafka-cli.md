---
post_title: Kafka CLI
menu_order: 105
---
You can run and manage Kafka jobs by using the Kafka CLI.

`--help`, `-h`

:   Show a description of all command options and positional arguments for the command.

`--info`

:   Show a brief description of the command.

`--version`

:   Show the version of the installed Kafka CLI.

`broker add`

:   Add a new broker.

`broker list`

:   Show the active brokers.

`broker remove`

:   Remove a broker.

`broker update <id-expr> [options]`

:   Update an existing broker. For command syntax and options, type `dcos kafka update --help` on the command line.

`broker start`

:   Start a broker.

`broker stop`

:   Stop a broker.

`topic add`

:   Add a topic.

`topic list`

:   List the topics.

`topic rebalance`

:   Rebalance the topics.

`topic update`

:   Update a topic.

For more information, see the [Kafka CLI][1] documentation.

 [1]: https://github.com/mesosphere/dcos-kafka