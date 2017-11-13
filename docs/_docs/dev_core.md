---
layout: doc
title: Build Core Platform
permalink: /docs/dev_core/

---

The Core Platform is the connector's application layer part for managing containers, message routes, and usage control policies. It is based on the Java Karaf framework and can be started as a standalone application, within a docker container or as a trust\|me _a0_ container.

##### Prerequisites

* Java 8 (7 won't work, 9 might need extra tweaking of the build)
* Maven (>= 3.3.9 recommended)
* npm (>= 5.5.1 recommended)
* docker (>= 17.05.0 recommended)

##### Checkout

Clone the project from Github.

```
git clone git@github.com:industrial-data-space/trusted-connector.git
```

##### Build

Change into the cloned directory and start the build.

```
mvn clean install
```

After a successful build, the Core Platform can be launched with the following command:

```
karaf-assembly/target/assembly/bin/karaf clean debug
```

Confirm that the management console is available at `http://ids:ids@localhost:8181/`.


The following Docker images are created and can be started with `docker run -ti --rm --name core-platform <image name>`:

```
registry.netsec.aisec.fraunhofer.de/ids/core-platform:develop
registry.netsec.aisec.fraunhofer.de/ids/core-platform:develop-raspi
registry.netsec.aisec.fraunhofer.de/ids/ttpsim:develop
```

The first one is the actual Core Platform. The second one is the ARM-compatible image tailored to the Raspberry Pi (Raspbian Lite Stretch) platform. The third one is a simulator for the trusted third party (TTP) which is only needed for integration tests.
