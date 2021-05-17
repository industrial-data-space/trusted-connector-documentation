---
layout: doc
title: Building the Core Platform
permalink: /docs/dev_core/

---

The core platform is the connector's application layer part for managing containers, message routes, and usage control policies. It is based on the Apache Karaf framework and can be started as a standalone application, within a docker container or as a trust\|me _a0_ container.

## Prerequisites

The following tools are required to build the karaf runtime and additional build artifacts and/or the docker images:

* docker (>= 17.05.0 recommended)
* docker-compose (>= 1.25.0 recommended)

In order to run the core platform outside the docker image as described below, a JVM supporting Java 11 (OpenJDK 11 recommended) is additionally required.

If you want to build the core platform without the `build-container` (used by `build.sh`, **not** recommended) need the following tools are required as well:

* Java Development Kit (JDK) 11
* Maven (>= 3.5.3 recommended)
* nodejs (>= 12 recommended)
* protobuf-compiler (or other package that provides `protoc`)

## Checkout

Clone the project from Github.

```
git clone https://github.com/industrial-data-space/trusted-connector.git
```

## Build

Change (`cd`) into the cloned directory, then start the build using the `build.sh` script:

```
chmod +x build.sh  # Make build.sh executable
./build.sh
```

The script will pull and start a dockerized build environment (`fraunhoferaisec/docker-build:develop`) and perform the gradle build.
You can specify custom build targets/flags for gradle, defaults are "build check --no-daemon --parallel".

After a successful build, the core platform can be launched with the following command:

```
karaf-assembly/build/assembly/bin/karaf clean debug
```

Confirm that the management console is available at `http://localhost:8181/`.

### Docker Build

If building of docker images from the artifacts built in the previous step is desired, you can either

* build them manually using the `Dockerfile` in the respective module directories (karaf-assembly/Dockerfile for the TC core, for instance) or
* enable experimental CLI features in docker (see https://docs.docker.com/engine/reference/commandline/cli/#experimental-features) and run `./buildx/docker-buildx.sh` (issue `chmod +x ./buildx/docker-buildx.sh` before if the script has not yet been marked executable). This will build all relevant images for the current platform.

## Entering the Shell

During development, you may want to interact with the shell of the application server. In a production environment, access to the shell must be limited for administrators or the shell must be removed completely. In this development setup, however, the shell can directly be accessed:

When running the core platform locally with `karaf-assembly/build/assembly/bin/karaf clean debug`, you are immediately dropped into the shell. 

If the core platform runs in a Docker container, use the following command to enter the shell.

```
docker exec -ti <container_name> /root/bin/client
```

## Managing Features

To keep its footprint low, the core platform only contains a few protocol adapters for Apache Camel, such as HTTP(S), WebSockets, and OPC-UA. If you need to install further protocol adapters, you may proceed as follows:

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
