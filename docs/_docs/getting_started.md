---
layout: doc
title: Getting Started
permalink: /docs/getting_started/

---

In this part you will learn how to download an instance of the Connector and start it on your computer.

> Note that this tutorial will use the (less secure) docker-based variant of the Connector. The alternative based on trustm3 comprises a whole OS stack, including the kernel.


## Prerequisites

The docker-based Connector requires any Linux operating system which supports docker. This is:

  - CentOS
  - Debian
  - Fedora
  - Oracle Linux
  - Red Hat Enterprise Linux
  - openSUSE and SUSE Linux Enterprise
  - Ubuntu
  - Gentoo
  - Arch
  - CRUX
  - Raspbian

MacOS works too, if docker and docker-compose are installed. There is a comfortable installer, doing all the work for MacOS on the Docker home page.

> Note that Windows will not work (although it supports docker through virtualization) because we require UNIX domain socket communication.


## Install Docker

Install required packages

``` bash
$ sudo apt-get install apt-transport-https ca-certificates curl
```

Install PGP key of docker repository

``` bash
$ curl -fsSL https://yum.dockerproject.org/gpg | sudo apt-key add -
```

Add docker repository

``` bash
$ sudo add-apt-repository "deb https://apt.dockerproject.org/repo/ ubuntu-$(lsb_release -cs) main"
```

Load packages

``` bash
$ sudo apt-get update
```

Install docker

``` bash
$ sudo apt-get -y install docker-engine
```

## Install docker-compose

``` bash
$ curl -L "https://github.com/docker/compose/releases/download/1.10.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

``` bash
$ chmod +x /usr/local/bin/docker-compose
```

Confirm it works

``` bash
$ docker-compose --version
```

## Run a Local Connector Instance

1. Download the [docker-compose.yaml](../example-000/docker-compose.yaml) file and save it in a new folder.

1. Login to the docker registry, if not already done:
```bash
  docker login app-store.isst.fraunhofer.de:5000 -u username -p password
```
The account is known to all members of the Industrial Data Space Association. If you wish to get an account, please [send us a message](mailto:info@fraunhofer.aisec.de).

1. Start the connector:
```bash
$ docker-compose up
```

Congratulations, you just started your first connector! The webconsole of the connector is available at `http://<host>:8181/`.

## What did just happen?

You installed docker on your machine, downloaded and started a docker-compose definition. This pulled three different docker container images from a remote registry:

* Core Platform Container
* TPM 2.0 Simulator
* Remote Attestation Repository

The Core Platform container is the heart of the Trusted Connector. It is responsible for establishing communication to other connectors, routes messages between containers and configures the Trusted Connector.
The TPM 2.0 simulator is a software trusted platform module which is responsible for attesting the integrity of the stack.

> In a _real_ setup, the TPM is a hardware module, but for the moment we keep it simple.
