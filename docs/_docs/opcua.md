---
layout: doc
title: Consuming OPC-UA
permalink: /docs/opcua/

---

## TODO

> Orphaned Page. Possibly Incomplete. Either Link or Delete

In this tutorial you will read sensor measurements from an [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) (OPC-UA) sensor and forward them to a REST service:

## Start the Connector

Download the [trusted-connector-examples_develop.zip](https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_develop.zip?raw=true) file. Unzip the file into a directory of your choice, `cd` into the contained folder `example-getting-started` and start the connector with docker-compose:

```bash
$ wget https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_develop.zip?raw=true -O examples.zip
$ unzip examples.zip
$ cd example-getting-started
$ docker-compose up
```

## Install an OPC-UA Server App

1. Open the administration console at `http://iot-connector:8181` and open the _Apps_ view.
2. Search for a demo OPC-UA server app and install it
3. Start the OPC-UA app


## Set up a message route
