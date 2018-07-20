---
layout: doc
title: Overview
permalink: /docs/overview/

---

<h3 class="teal-text text-darken-1">A secure IoT gateway platform.</h3>

The Trusted Connector is an open IoT edge gateway platform and a reference implementation of the Industrial Data Space Connector. It is currently being standardized as DIN Spec 27070.

Use the Trusted Connector to connect sensors with cloud services and other Connectors, using a vast range of protocol adapters. Security mechanisms like secure boot, remote platform attestation and data usage control allow you stay in control over your data and support you in GDPR compliance and auditing. The Trusted Connector is open source and built on open standards to avoid vendor lock-in.


<h2>Main Features</h2>

<div class="row">
    <div class="col s12 m6">
        <div class="card hoverable">
            <div class="card-content">
                <span class="card-title">App Isolation</span>
                <p>Apps running in the Trusted Container are isolated from each other and from the Internet. This way, apps cannot leak data or harm each other or the Connector.</p>
            </div>
            <div class="card-action text-grey text-darken-4">
                <a href="#apps" class="text-grey text-darken-4">Learn more</a>
            </div>
        </div>
    </div>
    <div class="col s12 m6">
        <div class="card hoverable">
            <div class="card-content">
                <span class="card-title">Cross-Enterprise Identity Management</span>
                <p>Trusted Connectors authorize each other using a dynamic attribute provisioning service (DAPS). This allows easy cross-enterprise authorization without the hassle of X509v3 cross certification.</p>
            </div>
            <div class="card-action text-grey text-darken-4">
                <a href="#idm" class="text-grey text-darken-4">Learn more</a>
            </div>
        </div>
    </div>
</div>
<div class="row">
    <div class="col s12 m6">
        <div class="card horizontal">
            <div class="card-image">
                <img src="https://lorempixel.com/100/190/nature/6">
            </div>
            <div class="card-stacked">
                <div class="card-content">
                    <p>With the trust|me container management and secure boot, the Trusted Connector makes use of a TPM to gurantee the integrity of the software stack from bare metal to applications. A remote attestation protocol makes sure you can rely on your remote party's integrity.</p>
                </div>
                <div class="card-action text-grey text-darken-4">
                    <a href="#trustedplatform" class="text-grey text-darken-4">Learn more</a>
                </div>
            </div>
        </div>
    </div>
    <div class="col s12 m6">
        <div class="card hoverable">
            <div class="card-content">
                <span class="card-title">Data Usage Control</span>
                <p>Control the flow of data with LUCON policies and bind remote data usage to obligations. Rely on attested trustworthy platforms to enforce obligations and make sure your message routes comply with security policies and legislation.</p>
            </div>
            <div class="card-action text-grey text-darken-4">
                <a href="#lucon" class="text-grey text-darken-4">Learn more</a>
            </div>
        </div>
    </div>
</div>



<span style="height:30px;display:block;"></span>
<a name="apps"></a>
## App Isolation

tbd




<span style="height:30px;display:block;"></span>
<a name="trustedplatform"></a>
## Trusted Platform

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


<div style="text-align:center">
    <img width="640" src="../../assets/img/overview.png"/>
</div>


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



<span style="height:30px;display:block;"></span>

<a name="idm"></a>
## Cross-Enterprise Identity Management

### Identity tokens
Every connector needs three identity tokens:
* A device certificate (X.509v3)
* A TLS connection certificate (X.509v3)
* A 'Dynamic Attribute Token'  (OAuth Access Token)

<div style="width:50%;text-align:center">
    <img src="../../assets/img/certificate-architecture.png"/>
</div>

Every connector needs a certificate issued by the Device-CA. This certificate serves as the root of identity. The contents of this certificate are kept at minimum to avoid the need for later revocation in case of changing attributes. This certificate needs to be manually deployed during connector setup.

The TLS connection certificate is used for TLS tunneling. This certificate is automatically requested by the connector by interacting with a ACME server that is integrated into the TLS Sub-CA. So no manual deployment is needed.

The 'Dynamic Attribute Token' is an OAuth Access Token, signed by the Dynamic Attribute Provisioning Service (DAPS). This is a short-lived token that contains attributes that the connector possesses.

### Authorization workflow

When a resource on a connector is accessed, an access token needs to be presented by the requesting connector. This is performed automatically with a run of the IDSCP.

<div style="width:50%;text-align:center">
    <img src="../../assets/img/idm-workflow.png"/>
</div>

The general workflow is:
* A requesting connector presents its Device Certificate to the DAPS to receive a Dynamic Attribute Token (DAT).
* This DAT can be used to directly access a resource provided by another Connector.
An alternative configuration option would be to introduce a local or use-case specific Authorization Service that provides a custom access token. This is completely optional and thus marked grey.
* The respective token is then handed to a Connector with every resource access request.

### Dynamic Access Token issuance
<div style="width:50%;text-align:center">
    <img src="../../assets/img/token_exchange.png"/>
</div>

* A: call C3/token endpoint with Client Credentials (X.509 Cert)
* B: Issue JWT-1\{attribute_list, client_id, aud: idsAS:\*\}
* C (optional): Hand in JWT-1, request JWT-2 \{scope: C1/PS\}
* D: Use Rule Engine for access decision, issue JWT-2\{aud: C1\}
* E: Connect via IDSCP (Auth by JWT-2)

An example for the attribute list would be certification status or membership in the IDS.

<a name="lucon"></a>
## Data Usage Control

tbd

<span style="height:30px;display:block;"></span>
