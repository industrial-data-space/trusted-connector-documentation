---
layout: doc
title: FAQ
permalink: /docs/dev_faq/

---

The following questions (FAQ) are frequently asked by people setting up the Trusted Connector.

- [When switching my system Java version, the gradlew build command still uses its own Java-Version](#when-switching-my-system-java-version-the-gradlew-build-command-still-uses-its-own-java-version)
- [I can't build the system using the latest node version 12 (2019 05)](#i-cant-build-the-system-using-the-latest-node-version-12-2019-05)
- [What Java Version is currently supported?](#what-java-version-is-currently-supported)

## When switching my system Java version, the gradlew build command still uses its own Java-Version

The first gradlew is used and installed it will check which Java-Version is available on the system and install its own version of Java of the same version. If people switch the system's Java Version, then gradlew requires the option:

- `./gradlew clean install --parallel -Dorg.gradle.java.home=/path_to_jdk_directory`
- Additional discussion can be found in threads like this: https://stackoverflow.com/questions/18487406/how-do-i-tell-gradle-to-use-specific-jdk-version

## I can't build the system using the latest node version 12 (2019 05)

Building the Trusted Connector using node 12 may result in errors in the build of the webconsole. If you see error similar to the following, we recommend using node 11 instead of 12. 

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

The Trusted Connector can currently be built using OpenJDK 8 and Oracle's JDK 8. We are currently working on migrating to OpenJDK 11.
