---
layout: doc
title: Overview
permalink: /docs/overview/

---

<h3 class="teal-text text-darken-1">A secure IoT gateway platform</h3>

The Trusted Connector is an open IoT edge gateway platform and a reference implementation of the Industrial Data Space Connector. It is currently being standardized as DIN Spec 27070.

Use the Trusted Connector to connect sensors with cloud services and other Connectors, using a vast range of protocol adapters. Security mechanisms like secure boot, remote platform attestation and data usage control allow you stay in control over your data and support you in GDPR compliance and auditing. The Trusted Connector is open source and built on open standards to avoid vendor lock-in.


<h3 class="teal-text text-darken-1">Main Features</h3>

<div class="row">
    <div class="col s12 m6">
        <div class="card horizontal" style="height:17em">
            <div class="card-image valign-wrapper" style="padding:10px;">
                <i class="fa fa-cubes" style="font-size:70px"></i>
            </div>
            <div class="card-stacked">
                <div class="card-content">
                    <span class="card-title">App Isolation</span>
                    <p>Apps running in the Trusted Container are isolated from each other and from the Internet. This way, apps cannot leak data or harm each other or the Connector.</p>
                </div>
                <div class="card-action text-grey text-darken-4">
                    <a href="#apps" class="text-grey text-darken-4">Learn more</a>
                </div>
            </div>
        </div>
    </div>
    <div class="col s12 m6">
        <div class="card horizontal" style="height:17em">
            <div class="card-image valign-wrapper" style="padding:10px;">
                <i class="fa fa-user" style="font-size:70px"></i>
            </div>
            <div class="card-stacked">
                <div class="card-content">
                    <span class="card-title">Cross-Enterprise Identity Management</span>
                    <p>Trusted Connectors authorize each other using a dynamic attribute provisioning service (DAPS). This allows for easy cross-enterprise authorization.</p>
                </div>
                <div class="card-action text-grey text-darken-4">
                    <a href="#idm" class="text-grey text-darken-4">Learn more</a>
                </div>
            </div>
        </div>
    </div>
</div>
<div class="row">
    <div class="col s12 m6">
        <div class="card horizontal" style="height:17em">
            <div class="card-image valign-wrapper" style="padding:10px;">
                <i class="fa fa-microchip" style="font-size:70px"></i>
            </div>
            <div class="card-stacked">
                <div class="card-content">
                    <span class="card-title">Trusted Platform</span>
                    <p>With the trust|me container management and secure boot, the Trusted Connector makes use of a TPM to gurantee the integrity of the software stack from bare metal to applications. A remote attestation protocol makes sure you can rely on your remote party's integrity.</p>
                </div>
                <div class="card-action text-grey text-darken-4">
                    <a href="#trustedplatform" class="text-grey text-darken-4">Learn more</a>
                </div>
            </div>
        </div>
    </div>
    <div class="col s12 m6">
        <div class="card horizontal" style="height:17em">
            <div class="card-image valign-wrapper" style="padding:10px;">
                <i class="fa fa-microchip" style="font-size:70px"></i>
            </div>
            <div class="card-stacked">
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
</div>



<span style="height:50px;display:block;"></span>
<a name="apps"></a>
## App Isolation

Apps come in the form of docker image templates and run in the Trusted Connector. By default, application containers are initially isolated from each other and restricted in a virtual network with the Core Platform container. This way, apps cannot influence each other and malicious apps cannot leak data to the Internet. Instead, all data must pass the usage control framework of the Core Platform which enforces security- and usage policies. This is a major advantage over other, purely OSGi-based application frameworks.

The strength of the isolation depends on the container management layer. Two container management layers are currently supported: [Docker](https://www.docker.com/) and [trustme](https://github.com/trustm3/trustme_main). 


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
                <li style="list-style-type: disc">Support for remote attestation &amp; platform integrity measurement of the <a href="#trustedplatform">trusted platform</a></li>
                <li style="list-style-type: disc">Support for ARM, x86, PowerPC architectures</li>
            </ul>
        </td>
    </tr>
</table>

No matter whether Docker or trust\|me is used, any image from the Trusted Connector App Store or from any Docker registry can be loaded into the Trusted Connector and launched as an application. With trust\|me, Docker images must be converted into a _trust\|me guestOS_ format before installation, using the automated [converter tool](docs/convert_docker).


<span style="height:50px;display:block;"></span>
<a name="trustedplatform"></a>
## Trusted Platform


<div style="text-align:center">
    <img src="../../assets/img/overview.png"/>
</div>

The Trusted Connector features the secure container management layer _trust\|me_ as an alternative to Docker. Though its basic mechanisms are similar to Docker (namespaces, cgroups and chroot), trust\|me was developed as a security architecture including secure boot, platform integrity measurements, and a hardened kernel. 

For mandatory access control, trust\|me adds a custom Linux Security Module (LSM) to the Linux kernel, which can be combined with SELinux using LSM stacking. In addition, the trust\|me architecture includes a secure element for tamper-proof key storage and a TPM 2.0 interface for (remote) attestation of the platform integrity using the IDSCP protocol.

For a more detailed description of the architecture see our publication _Manuel Huber, Julian Horsch, Michael Velten, Michael Weiss, Sascha Wessel. [A Secure Architecture for Operating System-Level Virtualization on Mobile Devices](http://link.springer.com/chapter/10.1007/978-3-319-38898-4_2). In Information Security and Cryptology 2016, pp. 430-450_


__Trusted Container Management Layer__

tbd

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




<span style="height:50px;display:block;"></span>

<a name="idm"></a>
## Cross-Enterprise Identity Management

Trusted Connectors retrieve X509v3 certificates from an ACME server - either from the widely used [letsencrypt](https://www.letsencrypt.org) service or from a dedicated ACME server. These certificates do not give any guarantees about the connector, apart from the correctness of its hostname. They can thus be used for encrypted communication, but neither for authentication nor access control.

For access control, authentic and descriptive attributes are issued by an external Dynamic Attribute Provisioning Service (DAPS) in the form of [JWT](https://jwt.io/) tokens. Trusted Connectors connect to the central DAPS, present their X509v3 certificate and receive an identity token that includes authentic attributes, attested by the DAPS. The attributes are automatically transmitted to the remote Trusted Connector over the IDSCP protocol and can be used to control access requests.

With the IDSCP protocol, this process is completely automated and transparent to the user. The central DAPS acts as a cross-enterprise identity provider without the need to set up cross-certified public key infrastructures.

<span style="height:50px;display:block;"></span>

<a name="lucon"></a>
## Data Usage Control

Traditional access control only regulates _if_ data may be accessed. However, in data-centric IoT systems and with fine-granular data protection legislation in place, it is even more important to control _how_ data is used. The Trusted Connector includes LUCON (Logic based Usage CONtrol), a data usage control engine that controls data flows between apps and the Internet and binds the usage of data to _obligations_ which must be enforced either locally in the Trusted Connector or on the trusted remote platform.

LUCON is a simple policy language for message labeling and taint tracking and comes with a policy authoring kit based on the Eclipse IDE. Compiled policies are loaded into the Trusted Connector and control the way how messages are handled by the Apache Camel routing engine.

Furthermore, message routes can be validated against LUCON policies a priori. That is, the Trusted Connector will evaluate whether any of its message routes could under any circumstances violate the policies and if so, generate an example of a violating message trace. This helps users in understanding potential misconfigurations of their message routes and allows auditors to generate proofs of compliance with data protection policies.

<span style="height:50px;display:block;"></span>