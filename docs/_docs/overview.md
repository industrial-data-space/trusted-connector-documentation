---
layout: doc
title: Overview
permalink: /docs/overview/

---

The IDS Trusted Connector is an IoT and Industrial 4.0 edge gateway platform and a reference implementation of the Industrial Data Space Connector that is also being standardized as DIN Spec 27070.

The Trusted Connector allows connecting sensors with cloud services and other Connectors, using various protocol adapters and configurable data routes. Data procession applications can be loaded into the connector using containerization frameworks (Docker or trustme).

The architecture comprises three main building blocks:
- Operating system and container management layer
- Core Platform container
- Application containers

__Operating system & container management layer__: The Trusted Connector supports two container management layers: [Docker](https://www.docker.com/) and [trustme](https://github.com/trustm3/trustme_main). With Docker support, the Trusted Connector can run on virtually any platform, from Raspberry Pi to large clusters in the cloud. trustme is a stripped-down containerization framework that has its origins in a secure Android smartphone and provides security mechanisms like secure boot, mandatory access control, platform integrity measurement, and remote attestation. By supporting any of the two container management layers, the Trusted Connector can be bundled as a flexible large-scale cloud service or as a locked-down highly secure embedded gateway OS.

__Core Platform container__: The Core Platform container provides the main functionality of the Trusted Connector and is the sole intermediate between application containers and the Internet. It runs on top of the container management layer and hosts different services for managing applications, setting up data routes and connecting to other Trusted Connectors. Based on Apache Karaf, it provides a management shell and REST API. The main services are

* __Usage Control__: Data flows and the usage of messages can be controlled using the [LUCON](docs/usage_control) policy framework.
* __Route Manager__: Message routing based on [Apache Camel](http://camel.apache.org/). The basic installation contains protocol adapters for
    * {:.browser-default} IDSP: An HTTPS-based session protocol between Trusted Connectors, including remote attestation and trust negotiation
    * OPC-UA (based on Eclipse Milo)
    * MQTT (based on Eclipse Paho)
    * ActiveMQ
    * JMS
    * REST
    * HTTP(S)
    * Websockets
    Apache Camel supports more than 200 [protocol adapters](http://camel.apache.org/components.html) which can be used by the Trusted Connector.
* __Configuration Manager__: Persistent key/value store for configurations of the Core Platform, based on the OSGi Preferences Service.
* __IDS Protocol__: The IDS protocol establishes trust and sets up a secure messaging channel between Trusted Connectors. Integrity of platform stacks are measured and remotely attested, trust levels and usage control policies are negotiated between endpoints. The IDS protocol is available as an endpoint in Apache Camel routes.
* __Webconsole__: An administration web interface

__Application containers__: Applications come in the form of Docker containers. No matter whether the Trusted Connector runs on Docker or trustme management layer, any image from a Docker registry can be pulled into the Trusted Connector and launched as an application. Without further configuration, application containers are isolated from each other and restricted in a virtual network with the Core Platform which blocks outbound traffic. That is, even malicious applications cannot interfere with the running system. This is a major advantage over other, purely OSGi-based application frameworks.