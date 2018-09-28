---
layout: doc
title: Connect REST Apps Using the IDS
permalink: /docs/rest/

---

In this tutorial you will set up a minimal but representative scenario of parties exchanging data over their Trusted Connectors. It will include the following components:

* a data _provider_ that uses an app running on the Trusted Connector stack to receive sensor data from an MQTT broker and forward it over the IDS protocol to the consumer.
* a data _consumer_ that receives data from the provider over the IDS protocol, transforms it into a REST call and sends it to web application running on the Trusted Connector stack.
* a _trusted third party_ (TTP) that maintains a list of trusted PCR registers.

##### Hosting Apps

The scenario includes two data apps: a __Provider App__ which retrieves sensor data from outside of the IDS and provides it as a REST service and a __Consumer App__ which retrieves data via REST and displays it in a webpage. The scenario demonstrates how any application can be hosted by the Trusted Connector, as long as it is wrapped in a Docker or trustme container

* no matter which language it is programmed in
* no matter whether it has been written to run in an IDS connector or on any other platform. Apps do not have to be IDS-aware - in this scenario they use simple REST interfaces.

<div style="text-align:center">
	<img alt="Connecting two REST services to transfer MQTT data" src="../..//assets/img/example-0001.png" /><br>
</div>

##### Remote Attestation with the IDS Protocol

Communication in this scenario runs over the secure IDSC protocol. IDSC is a subprotocol of WSS (Web Socket Security) and includes a remote attestation and meta data exchange when establishing a session between two endpoints. The remote attestation confirms that both endpoints are in a trusted state, i.e. that they run known and approved components.


##### Data Conversion

An (example) sensor provides measurement data via MQTT messages. A data app running in the __Provider Connector__ subscribes to MQTT messages and provides them via a REST interface. The connector retrieves it from the REST interface and sends it over the secure IDS Communication protocol (IDSC) to the __Consumer Connector__. There, it is displayed in a web page.

So, the conversion is: _MQTT -->  REST (text/plain) --> IDSP (binary blob) --> REST (text/plain) --> HTML_

## Unzip Example Setup

Download the [trusted-connector-examples_1.0](https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_1.0.zip?raw=true) file. Unzip the file in some folder and go to the contained folder `example-010`.

This folder contains docker-compose descriptions for three main entities: the _Provider Connector_, the _Consumer Connector_, and the _Trusted Third Party_. In a production setup, these three entities would be remotely connected and operated by different owners. For the sake of this example, we will run them on your local machine.


## Start Trusted Third Party

Start the Trusted Third Party (TTP) which holds trusted PCR values for remote attestation.

``` bash
$ docker-compose -f docker-compose-ttp.yaml up
```

## Start Provider

Start the __Provider Connector__. It includes a simple node app which subscribes to temperature values via MQTT and provides them as REST service. A message route will connect to this REST interface, receive the values and push them to the __Consumer Connector__ over the IDSC protocol. The _Provider Connector_ also includes a TPM daemon and a TPM 2.0 simulator for remote attestation.

``` bash
$ docker-compose -f docker-compose-provider.yaml up
```

## Start Consumer

Start the __Consumer Connector__. It receives sensor values over the secured and remotely attested IDSC protocol and forwards it to a simple node app running in the __Consumer Connector__. The node app receives data via HTTP POST requests and displays it in a web page.

``` bash
$ docker-compose -f docker-compose-consumer.yaml up
```

The consumer app exposes a web interface. Open the application's web page at `http://localhost:8081`. You should see temperature values which have been received over the IDSC protocol.
