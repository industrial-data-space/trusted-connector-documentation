---
layout: doc
title: IDSCP2 Overview
permalink: /docs/idscp_overview/

---


Parties in the IDS can use the _IDS Communication Protocol (IDSCP)_ to exchange data over a secure channel. IDSCP underwent
some major redesign and is now available as IDSCP2.

IDSCP2 design principles:
* IDSCP2 is highly modular. Users and developers can choose one of the available modules for security tokens, secure tunneling or remote attestation. Or they can implement their own drivers and modules.
  * IDSCP2 can use security tokens. With the security token, a connector can proof its attributes to other instances.
  * IDSCP2 data exchange is based on the creation of a secure tunnel. So all data is exchanged in an authenticated, confidential and integrity protected manner.
  * IDSCP2 supports remote integrity verification and binds the secure tunnel to this verification process. All exchanged data is bound to this raised trust level.
* Metadata is exchanged only at the moment of tunnel establishment and when needed. Overhead is reduced and enables applications such as streaming high volume data.
* IDSCP2 is compatible to IDS applications, but can also be used without IDS dependencies.
* The application layer can be designed on top of the base protocol and supports IDS messaging as well as arbitrary message types.


<div style="text-align:center">
	<img src="../../assets/img/idscp2_overview.png"/>
</div>

The protocol establishment can be separated into these steps:

>A) A secure tunnel is established, performing authentication based on a trust anchor such as X.509 certificates. \
B) Metadata is exchanged, such as available remote attestation schemes. For IDS applications, Dynamic Attribute Tokens are exchanged.\
C) Remote attestation is performed. The resulting state is bound to the tunnel. \
D) The tunnel is unlocked and application data can be exchanged bidirectionally.

The payload format is not defined by IDSCP so it can be used as a mere transport layer, leaving it up to the application to send XML, Protobuf, JMS, MQTT, OPC-UA, or any other OSI-layer 7 protocol.

#### Secure tunnel
To guarantee information exchange in an authenticated, confidential and integrity protected manner, a secure tunnel is created before any further information is exchanged. In the current implementation, TLS 1.3 is used as a default. However, it is possible to use other means to create a secure tunnel. Authentication is performed using a trust anchor. In the current implementation, this is bound to a X.509 certificate. This can be extendend or changed to other authentication modes as well. 

#### Remote Attestation
In order to raise the level of trust between communication partners, remote integrity verification of software stacks and verification of activated security features or other properties is desirable. A common approach to enable remote attestation is to facilitate a hardware chip present in many modern system architectures, the Trusted Platform Module (TPM). This allows to guarantee trusted boot chains and secure boot as well as a remote verification of the boot process. In some cases, a chip bound to a specific platform appears to offer not enough flexibility. Current and upcoming processor based, so called "Confidential Computing" environments feature isolation of software components and also allow for integrity verification as well as remote attestation. Examples for this are Intel SGX and AMD SEV. These kind of hardware security mechanisms are depicted as "Hardware Security" in the illustration above.
IDSCP2 follows a driver-based design approach. In addition to the already implemented remote attestation schemes (e.g., TPM2.0), it allows for custom driver design to support upcoming remote attestation flows such as AMD SEV SNP.

#### Security token
IDS communication is based on X.509 certificates for the authentication of the secure tunnel. On top of this, dynamic attributes and extended IDS attributes are covered by handing in a so-called Dynamic Attribute Token. Details for technical IDS specifics can be found in the [IDS-G](https://github.com/International-Data-Spaces-Association/IDS-G/tree/main/Components/IdentityProvider/DAPS).


#### IDS feature set
For IDS applications (such as the use with a Trusted Connector), the protocol is configured as a TLS-based protocol that establishes a bidirectional session between connectors and allows to send any payloads. IDS Messages are exchanged as part of the application layer. 

Instead of using a full-blown Connector, the protocol can also be used by standalone applications either acting as server or as client.
