---
layout: doc
title: Consuming OPC-UA
permalink: /docs/opcua/

---

In this tutorial you will read sensor measurements from an OPC-UA sensor and forward them to a REST service:

## Start the Connector

Download the [docker-compose.yaml](https://raw.githubusercontent.com/industrial-data-space/trusted-connector/develop/docker-compose.yaml) file and start the connector:

```bash
$ wget https://raw.githubusercontent.com/industrial-data-space/trusted-connector/develop/docker-compose.yaml
$ docker-compose up
```

## Install an OPC-UA Server App

1. Open the administration console at `http://iot-connector:8181` and open the _Apps_ view.
2. Search for a demo OPC-UA server app and install it
3. Start the OPC-UA app


## Set up a message route
