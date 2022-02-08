---
layout: doc
title: Convert docker images
permalink: /docs/convert_docker/

---

The `converter` tool converts docker images into trustme format. See [here](../../docs/dev_setup/#docker-converter) how to build the `converter` tool from source.

Converting a docker image from the public docker registry is easy. For example, to pull the image `library/debian:stable", run the following command:

```
$ ./converter pull "registry-1.docker.io" "library/debian"
```

It is also possible to convert images from private docker registries:

```
$ ./converter login -u "<USER>" -p "<PASSWD>" "mvn.ids.isst.fraunhofer.de:5000"
$ ./converter pull "mvn.ids.isst.fraunhofer.de:5000" "ids/core-platform"
```
