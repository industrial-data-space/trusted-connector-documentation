---
layout: doc
title: Connect REST Apps Using the IDS
permalink: /docs/rest/

---

*This documentation refers to example-010. Older examples (001 and 009) may not be runnable and use a different construction on the provider side: A node app receives the produced MQTT messages and provides them to the connector via a REST interface.*

In this tutorial you will set up a minimal but representative scenario of parties exchanging data over their Trusted Connectors. It will include the following components:

* a data _provider_ that receives sensor data from a MQTT broker and forwards it over the IDS protocol to the consumer.
* a data _consumer_ that receives data from the provider over the IDS protocol, transforms it into a REST call and sends it to a web application running on the Trusted Connector stack.
* a _trusted third party_ (TTP) that maintains a list of trusted PCR registers for remote attestation.

##### Hosting Apps

The scenario includes a __Consumer App__ which retrieves data via REST and displays it in a webpage. The scenario demonstrates how any application can be hosted by the Trusted Connector, as long as it is wrapped in a Docker or trustme container

* no matter which language it is programmed in
* no matter whether it has been written to run in an IDS connector or on any other platform. Apps do not have to be IDS-aware - in this scenario they use a simple REST interface.

<div style="text-align:center">
	<img alt="Transfering MQTT data to a rest service" src="../..//assets/img/example-0001.png" /><br>
</div>

##### Remote Attestation with the IDS Communication Protocol

Communication in this scenario runs over the secure IDS Communication Protocol (IDSCP). IDSCP is a subprotocol of WSS (Web Socket Security) and includes a remote attestation and meta data exchange when establishing a session between two endpoints. The remote attestation confirms that both endpoints are in a trusted state, i.e. that they run known and approved components.

##### Data Conversion

An (example) sensor provides measurement data via MQTT messages. The __Provider Connector__ retrieves it from the MQTT broker and sends it over the secure IDSCP to the __Consumer Connector__. There, it is displayed in a web page.

So, the conversion is: _MQTT -->  IDSCP (binary blob) --> REST (text/plain) --> HTML_


## Unzip Example Setup

Download the [trusted-connector-examples_1.0](https://github.com/industrial-data-space/trusted-connector/blob/develop/examples/trusted-connector-examples_1.0.zip?raw=true) file. Unzip the file in some folder and go to the contained folder `example-010`.

This folder contains docker-compose descriptions for three main entities: the _Provider Connector_, the _Consumer Connector_, and the _Trusted Third Party_. In a production setup, these three entities would be remotely connected and operated by different owners. For the sake of this example, we will run them on your local machine.


## Start Trusted Third Party

Start the Trusted Third Party (TTP) which holds trusted PCR values for remote attestation.

``` bash
$ docker-compose -f docker-compose-ttp.yaml up
```


## Start Provider

Start the __Provider Connector__. It includes a simple node app which provides temperature values via a MQTT topic. A message route will connect to this MQTT topic, receive the values and push them to the __Consumer Connector__ over the IDSCP. The _Provider Connector_ also includes a TPM daemon and a TPM 2.0 simulator for remote attestation simulation.

``` bash
$ docker-compose -f docker-compose-provider.yaml up
```


## Start Consumer

Start the __Consumer Connector__. It receives sensor values over the secured and remotely attested IDSCP and forwards it to a simple node app running in the __Consumer Connector__. The node app receives data via HTTP POST requests and displays it in a web page.

``` bash
$ docker-compose -f docker-compose-consumer.yaml up
```

The consumer app exposes a web interface. Open the application's web page at `http://localhost:8081`. You should see temperature values which have been received via the IDSCP.
