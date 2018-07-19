---
layout: doc
title: Overview
permalink: /docs/overview/

---

_A secure IoT gateway platform._

The IDS Trusted Connector is an IoT and Industrial 4.0 edge gateway platform and a reference implementation of the Industrial Data Space Connector that is also being standardized as DIN Spec 27070.

The Trusted Connector allows connecting sensors with cloud services and other Connectors, using various protocol adapters and configurable data routes. Data procession applications can be loaded into the connector using containerization frameworks.

<div class="row center" style="padding: 40px"><a class="btn waves-effect waves-light" href="../../assets/2018-trusted-connector-whitepaper.pdf">Whitepaper (German)</a></div>


<div style="text-align:center">
    <img src="../../assets/img/overview.png"/>
</div>



__Operating system & container management layer__

The Trusted Connector supports two container management layers: [Docker](https://www.docker.com/) and [trustme](https://github.com/trustm3/trustme_main). 

<table>
	<tr>
		<td>
			<a title="By dotCloud, Inc. [Apache License 2.0 (http://www.apache.org/licenses/LICENSE-2.0)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File%3ADocker_(container_engine)_logo.png"><img width="128" alt="Docker (container engine) logo" src="https://upload.wikimedia.org/wikipedia/commons/7/79/Docker_%28container_engine%29_logo.png"/></a>
		</td>
		<td>
			<ul>
				<li style="list-style-type: disc">For standard Linux stacks, leveraging the powerful Docker ecosystem</li>
				<li style="list-style-type: disc">Support for x86 and Raspberry Pi (ARM) platforms</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td><img width="128" src="../../assets/img/trustme-logo.png"/></td>
		<td>
			<ul>
				<li style="list-style-type: disc">For highly secure embedded platforms</li>
				<li style="list-style-type: disc">Support for remote attestation &amp; platform integrity measurement</li>
				<li style="list-style-type: disc">Support for ARM, x86, PowerPC architectures</li>
			</ul>
		</td>
	</tr>
</table>


__Core Container__

The Core Container provides the main functionality of the Trusted Connector and is the sole intermediate between application containers and the Internet. It runs on top of the container management layer and hosts different services for managing applications, setting up data routes and connecting to other Trusted Connectors. Based on Apache Karaf, it provides a management shell and REST API. The main services are

* __Usage Control__: Data flows and the usage of messages can be controlled using the [LUCON](../usage_control) policy framework.
* __Route Manager__: Message routing based on [Apache Camel](http://camel.apache.org/). The basic installation contains protocol adapters for
    * {:.browser-default} IDSP: A secure web socket protocol between Trusted Connectors, including remote attestation and meta data exchange
    * OPC-UA (based on Eclipse Milo)
    * MQTT (based on Eclipse Paho)
    * JMS
    * REST
    * HTTP(S)
    * Websockets
    
    Apache Camel supports more than 200 [protocol adapters](http://camel.apache.org/components.html) which can be used by the Trusted Connector.
* __IDS Protocol__: The IDSC protocol establishes trust and sets up a secure messaging channel between Trusted Connectors. Integrity of platform stacks are measured and remotely attested, trust levels and usage control policies are negotiated between endpoints. The IDSC protocol is available as an endpoint in Apache Camel routes.
* __Management GUI__: An administration web interface

__Application containers__

Applications come in the form of Docker containers. No matter whether Docker or trustme is used, any image from a Docker registry can be pulled into the Trusted Connector and launched as an application. Without further configuration, application containers are initially isolated from each other and restricted in a virtual network with the Core Platform which blocks outbound traffic. That is, even malicious applications cannot interfere with the running system. This is a major advantage over other, purely OSGi-based application frameworks.