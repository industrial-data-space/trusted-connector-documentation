---
layout: doc
title: FAQ
permalink: /docs/dev_faq/

---

The following questions (FAQ) are frequently asked by people setting up the Trusted Connector.

- [I found a bug](#i-found-a-bug)
- [Do I have to build the Trusted Connector to try out the examples?](#do-i-have-to-build-the-trusted-connector-to-try-out-the-examples)
- [I have connectivity issues](#i-have-connectivity-issues)
- [When switching my system Java version, the gradlew build command still uses its own Java-Version](#when-switching-my-system-java-version-the-gradlew-build-command-still-uses-its-own-java-version)
- [I can't build the system using the latest node version 12 (2019 05)](#i-cant-build-the-system-using-the-latest-node-version-12-2019-05)
- [What Java Version is currently supported?](#what-java-version-is-currently-supported)


## I found a bug

If you run into any bugs, please [file a bug report on GitHub](https://github.com/industrial-data-space/trusted-connector/issues). We will check the issue and reply as soon as possible.

## Do I have to build the Trusted Connector to try out the examples?

We provide the Core Platform of the Trusted Connector as ready-to-run docker images for `linux/amd64`, `linux/arm/v7` and `linux/arm64/v8`. [Just head over to DockerHub to get started](https://hub.docker.com/r/fraunhoferaisec/trusted-connector-core).

The `develop` tags are our bleeding edge builds that run whenever we push to the respective git branch. You may encounter errors with this version. If you need a stable environment, please use the `latest` image which reflects the most recent stable release version.

If you want to run the Core Platform on the underlying trustme OS, you will need to build a bootable trustme image [as explained here](https://industrial-data-space.github.io/trusted-connector-documentation/docs/dev_trustme/).

## I have connectivity issues

When experiencing connectivity issues during docker build, gradle build, or even at runtime, 95% of the time there is some proxy or DNS filter installed in your corporation. Fixing that is possible, but tricky. Please look around on StackOverflow and similar sources for directions, or ask your IT department to provide your testing system direct access to the web. Docker container, for instance, will always try to resolve using Google DNS (8.8.8.8 and 8.8.4.4) by default, so make sure this DNS is reachable from your network.

## When switching my system java version, gradlew still uses another Java version

The gradlew script has some discovery algorithms to identify the Java version it uses. The Java version used by gradlew can be configured in one of the following ways:

- Set the JAVA_HOME environment variable to the path of the JDK to be used
- Temporarily override JAVA_HOME for the current shell: `JAVA_HOME=/path_to_jdk_directory; ./gradlew ...` (Linux bash example, slightly different for Mac OS)
- Use gradle's `org.gradle.java.home` parameter: `./gradlew ... -Dorg.gradle.java.home=/path_to_jdk_directory`

Discussions regarding this topic can be found in threads like this: https://stackoverflow.com/questions/18487406/how-do-i-tell-gradle-to-use-specific-jdk-version

## I can't build the system using node version 12 (2019 05)

Building of older versions of the Trusted Connector using node 12 may result in errors during the build of the webconsole module. If you see errors similar to the following, we recommend switching to a more recent version of the Trusted Connector or downgrading to node 11:

```coffee
...
../src/create_string.cpp: In function 'char* create_string(Nan::MaybeLocal<v8::Value>)':
../src/create_string.cpp:17:37: error: no matching function for call to 'v8::String::Utf8Value::Utf8Value(v8::Local<v8::Value>&)'
   v8::String::Utf8Value string(value);
...
gyp ERR! node-gyp -v v3.8.0
gyp ERR! not ok 
Build failed with error code: 1

> Task :ids-webconsole:yarnInstall FAILED

FAILURE: Build failed with an exception.
```

## What Java version is currently supported?

The Trusted Connector uses OpenJDK 11.