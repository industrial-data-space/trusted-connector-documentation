---
layout: doc
title: Setting up a Connector
permalink: /docs/getting_started/

---

In this part you will learn how to download an instance of the Connector and start it on your computer.

> Note that this tutorial will use the (less secure) docker-based variant of the Trusted Connector. The alternative based on trustme comprises a whole OS stack, including the kernel.

> Note that in order to run the Trusted Connector in a Virtual Machine, at least 2 CPUs must be assigned to the VM.

### Install Docker

Follow the [instructions from the Docker website](https://www.docker.com/community-edition#/download) to install Docker Community Edition.

Confirm it works

``` bash
$ docker --version
```


## Install docker-compose

Install docker-compose by following the [instructions on the website](https://docs.docker.com/compose/install/#prerequisites).

Confirm it works

``` bash
$ docker-compose --version
```

## Run a Local Connector

Download the [trusted-connector-examples_develop.zip](https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_develop.zip?raw=true) file. Unzip the file in some folder, `cd` into the contained folder `example-getting-started` and start the connector with docker-compose:

```bash
$ wget https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_develop.zip?raw=true -O examples.zip
$ unzip examples.zip
$ cd example-getting-started
$ docker-compose up
```

Congratulations, you just started your first connector! The webconsole of the connector is available at `http://localhost:8181/`.

The login is `ids`:`ids`.

## What did just happen?

You downloaded and started a docker-compose definition. This pulled the Trusted Connector core platform image and created a container from it.

The Core Platform container is the heart of the Trusted Connector. It is responsible for establishing communication to other connectors, routes messages between containers and configures the Trusted Connector.
