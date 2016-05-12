---
post_title: Customizing Your Spark Installation
menu_order: 105
---
# Installing the Spark app only, not CLI

In this example, the Spark app is installed without the Spark CLI:

    `$ dcos package install —app spark
    `

# Installing Spark with A Custom Name

In this example, Spark is installed with the explicit application ID `alice-spark` specified:

    `$ dcos package install --app-id=alices-spark spark
    Installing package [spark] version [1.4.0-SNAPSHOT] with app id [alices-spark]
    Installing CLI subcommand for package [spark]
    Spark cluster mode on Mesos is ready!
    `