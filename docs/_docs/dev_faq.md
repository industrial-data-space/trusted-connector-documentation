---
layout: doc
title: FAQ
permalink: /docs/dev_faq/

---

The following questions (FAQ) are frequently asked by people setting up the Trusted Connector.

- [When switching my system Java version, the gradlew build command still uses its own Java-Version](#when-switching-my-system-java-version-the-gradlew-build-command-still-uses-its-own-java-version)
- [I can't build the system using the latest node version 12 (2019 05)](#i-cant-build-the-system-using-the-latest-node-version-12-2019-05)
- [What Java Version is currently supported?](#what-java-version-is-currently-supported)

## When switching my system java version, gradlew still uses another Java version

The gradlew script has some discovery algorithms to identify the Java version it uses. The Java version used by gradlew can be configured in one of the following ways:

- Set the JAVA_HOME environment variable to the path of the JDK to be used
- Temporarily override JAVA_HOME for the current shell: `JAVA_HOME=/path_to_jdk_directory; ./gradlew ...` (Linux bash example, slightly different for Mac OS)
- Use gradle's `org.gradle.java.home` parameter: `./gradlew ... -Dorg.gradle.java.home=/path_to_jdk_directory`

Discussions regarding this topic can be found in threads like this: https://stackoverflow.com/questions/18487406/how-do-i-tell-gradle-to-use-specific-jdk-version

## I can't build the system using node version 12 (2019 05)

Building the Trusted Connector using node 12 may result in errors during the build of the webconsole module. If you see errors similar to the following, we recommend switching to a more recent version of the Trusted Connector or downgrading to node 11:

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


## What Java Version is currently supported?

The Trusted Connector used to be built using OpenJDK 8 and Oracle's JDK 8.

Starting with version 3.0.0, both build process and runtime have been adapted for OpenJDK 11, which is now the recommended platform.
