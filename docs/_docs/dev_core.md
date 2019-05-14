---
layout: doc
title: Build Core Platform
permalink: /docs/dev_core/

---

The Core Platform is the connector's application layer part for managing containers, message routes, and usage control policies. It is based on the Java Karaf framework and can be started as a standalone application, within a docker container or as a trust\|me _a0_ container.

## Prerequisites

* Java 8 (7 won't work, 9 might need extra tweaking of the build)
* Maven (>= 3.5.3 recommended)
* yarn (>= 1.6.0 recommended)
* docker (>= 17.05.0 recommended)

## Checkout

Clone the project from Github.

```
git clone https://github.com/industrial-data-space/trusted-connector.git
```

## Build

Change into the cloned directory and start the build.

```
./gradlew dockerize --parallel -PdockerTag="develop"
```

After a successful build, the Core Platform can be launched with the following command:

```
karaf-assembly/build/assembly/bin/karaf clean debug
```

Confirm that the management console is available at `http://localhost:8181/`.


The following Docker images are created and can be started with `docker run -ti --rm --name core-platform <image name>`:

```
fraunhoferaisec/trusted-connector-core:develop
fraunhoferaisec/ttpsim:develop
```

The first one is the actual core platform. The second one is a simulator for the trusted third party (TTP) which is only needed for integration tests.


## Entering the Shell

During development, you may want to interact with the shell of the application server. In a production environment, access to the shell must be limited for administrators or the shell must be removed completely. In this development setup, however, the shell can directly be accessed:

When running the Core Platform locally with `karaf-assembly/build/assembly/bin/karaf clean debug`, you are immediately dropped into the shell. 

If the Core Platform runs in a Docker container, use the following command to enter the shell.

```
docker exec -ti <container_name> /root/bin/client
```

## Managing Features

To keep its footprint low, the Core Platform only contains a few protocol adapters for Apache Camel, such as HTTP(S), WebSockets, and OPC-UA. If you need to install further protocol adapters, you may proceed as follows:

Enter the shell as described above.

List available features. Grep for `camel-` to limit the list to protocol adapters:

```
feature:list|grep camel
```

To install the desired feature, simply use the `feature:install` command. For example, to install MQTT support from the Eclipse Paho package, enter

```
feature:install camel-paho
```

In the web dashboard, MQTT will now be listed as a supported component.
