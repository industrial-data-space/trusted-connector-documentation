---
layout: doc
title: FAQ
permalink: /docs/dev_faq/

---

The following questions (FAQ) are frequently asked by people setting up the Trusted Connector.

- [I heard about an IDSCP protocol based on WebSockets, where to find it?](#i-heard-about-an-idscp-protocol-based-on-websockets-where-to-find-it)
- [I found a bug](#i-found-a-bug)
- [Do I have to build the Trusted Connector to try out the examples?](#do-i-have-to-build-the-trusted-connector-to-try-out-the-examples)
- [I have connectivity issues](#i-have-connectivity-issues)
- [When switching my system's Java version, gradlew still uses another Java version](#when-switching-my-systems-java-version-gradlew-still-uses-another-java-version)
- [What Java Version is currently supported?](#what-java-version-is-currently-supported)


## I heard about an IDSCP protocol based on WebSockets, where to find it?

The old IDSCP (version 1) protocol was based on WebSockets.
However, its code was poorly written, poorly maintained, and typically relied on WebSocket libraries with lots of dependencies and overhead.

Due to this and other design decisions, IDSCP2 was newly implemented using TCP/IP directly. The old IDSCP protocol has been removed with TC release 4.0.0.
Due to its modular architecture, a WebSocket-based transport protocol for IDSCP2 could be implemented. However, this is not planned for the near future at the moment.

## I found a bug

If you run into any bugs, please [file a bug report on GitHub](https://github.com/industrial-data-space/trusted-connector/issues). We will check the issue and reply as soon as possible.

## Do I have to build the Trusted Connector to try out the examples?

We provide the Core Platform of the Trusted Connector as ready-to-run docker images for `linux/amd64`, `linux/arm/v7` and `linux/arm64/v8`. [Just head over to DockerHub to get started](https://hub.docker.com/r/fraunhoferaisec/trusted-connector-core).

The `develop` tags are our bleeding edge builds that run whenever we push to the respective git branch. You may encounter errors with this version. If you need a stable environment, please use the `latest` image which reflects the most recent stable release version.

If you want to run the Core Platform on the underlying trustme OS, you will need to build a bootable trustme image [as explained here](https://industrial-data-space.github.io/trusted-connector-documentation/docs/dev_trustme/).

## I have connectivity issues

When experiencing connectivity issues during docker build, gradle build, or even at runtime, 95% of the time there is some proxy or DNS filter installed in your corporation. Fixing that is possible, but tricky. Please look around on StackOverflow and similar sources for directions, or ask your IT department to provide your testing system direct access to the web. Docker container, for instance, will always try to resolve using Google DNS (8.8.8.8 and 8.8.4.4) by default, so make sure this DNS is reachable from your network.

## When switching my system's Java version, gradlew still uses another Java version

The gradlew script has some discovery algorithms to identify the Java version it uses. The Java version used by gradlew can be configured in one of the following ways:

- Set the JAVA_HOME environment variable to the path of the JDK to be used
- Temporarily override JAVA_HOME for the current shell: `JAVA_HOME=/path_to_jdk_directory; ./gradlew ...` (Linux bash example, slightly different for Mac OS)
- Use gradle's `org.gradle.java.home` parameter: `./gradlew ... -Dorg.gradle.java.home=/path_to_jdk_directory`

Discussions regarding this topic can be found in threads like this: https://stackoverflow.com/questions/18487406/how-do-i-tell-gradle-to-use-specific-jdk-version

## What Java Version is currently supported?

The Trusted Connector uses **OpenJDK 11** for both build and runtime.

The Trusted Connector used to be built using OpenJDK 8 or Oracle's JDK 8 before version 2.0.0, but this is not officially supported anymore.
You may change the Gradle build files for JDK 8 support at your own risk, don't expect support.