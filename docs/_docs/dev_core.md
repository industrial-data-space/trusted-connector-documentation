---
layout: doc
title: Building the Core Platform
permalink: /docs/dev_core/

---

The core platform is the connector's application layer part for managing containers, message routes, and usage control policies. It is based on the Spring Boot framework and can be started as a standalone application, within a docker container or as a trust\|me _a0_ container.

## Prerequisites 

The following tools are required to build the Spring Boot runtime and additional build artifacts and/or the docker images:

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


```bash
./gradlew :ids-connector:bootRun 
```

Confirm that the management console is available at `http://localhost:8080/`.

### Docker Build

If building of docker images from the artifacts built in the previous step is desired, you can either

* build them manually using the `Dockerfile` in the respective module directories (ids-connector/Dockerfile for the TC core, for instance) or
* enable experimental CLI features in docker (see https://docs.docker.com/engine/reference/commandline/cli/#experimental-features) and run `./buildx/docker-buildx.sh` (issue `chmod +x ./buildx/docker-buildx.sh` before if the script has not yet been marked executable). This will build all relevant images for the current platform.

