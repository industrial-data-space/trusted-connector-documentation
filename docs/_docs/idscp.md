---
layout: doc
title: Embedding the IDS protocol
permalink: /docs/idscp/

---

### Overview

Parties in the IDS can use the _IDS Communication Protocol (IDSCP)_ to exchange data over a secure channel. IDSCP underwent
some major redesign and is now available as IDSCP2.
IDSCP2 design principles:
* IDSCP2 data exchange is based on the creation of a secure tunnel. So all data is exchanged in an authenticated, confidential and integrity protected manner.
* Metadata is exchanged only at the moment of tunnel establishment and when needed. Overhead is reduced and enables applications such as streaming high volume data.
* IDSCP2 is compatible to IDS applications, but can also be used without IDS dependencies.
* IDSCP2 is highly modular. Users and developers can choose one of the available modules for security tokens, secure tunneling or remote attestation. Or they can implement their own drivers and modules.
* IDSCP2 supports remote integrity verification and binds the secure tunnel to this verification process. All exchanged data is bound to this raised trust level.
* The application layer can be designed on top of the base protocol and supports IDS messaging as well as arbitrary message types.


<div style="text-align:center">
	<img src="../../assets/img/idscp2_overview.png"/>
</div>

The protocol establishment can be separated into these steps:

1. A secure tunnel is established, performing authentication based on a trust anchor such as X.509 certificates.
2. Metadata is exchanged, such as available remote attestation schemes. For IDS applications, Dynamic Attribute Tokens are exchanged.
3. Remote attestation is performed. The resulting state is bound to the tunnel.
4. The tunnel is unlocked and application data can be exchanged bidirectionally.

The payload format is not defined by IDSCP so it can be used as a mere transport layer, leaving it up to the application to send XML, Protobuf, JMS, MQTT, OPC-UA, or any other OSI-layer 7 protocol.

#### Secure tunnel
To guarantee information exchange in an authenticated, confidential and integrity protected manner, a secure tunnel is created before any further information is exchanged. In the current implementation, TLS 1.3 is used as a default. However, it is possible to use other means to create a secure tunnel. Authentication is performed using a trust anchor. In the current implementation, this is bound to a X.509 certificate. Depending on the secure tunnel, other means can be facilitated.

#### Remote Attestation
In order to raise the level of trust between communication partners, remote integrity verification of software stacks and verification of activated security features or other properties is desirable. A common approach to enable remote attestation is to facility a hardware chip present in many modern system architectures, the Trusted Platform Module (TPM). This allows to guarantee trusted boot chains and secure boot as well as a remote verification about the boot process. Current and upcoming processor based, so called "Confidential Computing" environments feature isolation of software components and also allow for integrity verification as well as remote attestation. Examples for this are Intel SGX and AMD SEV. These kind of hardware security mechanisms are depicted as "Hardware Security" in the illustratio above.
IDSCP2 follows a driver-based design approach. In addition to the already implemented remote attestation schemes (e.g., TPM2.0), it allows for custom driver design to support upcoming remote attestation flows such as AMD SEV SNP.

#### IDS feature set
IDS communication is based on X.509 certificates for the authentication of the secure tunnel. On top of this, dynamic attributes and extended IDS attributes are covered by handing in a so-called Dynamic Attribute Token. (TODO Link)

For IDS applications (such as the use with a Trusted Connector), the protocol is configured as a TLS-based protocol that establishes a bidirectional session between connectors and allows to send any payloads.

Instead of using a full-blown Connector, the protocol can also be used by standalone applications either acting as server or as client.

This requires adding the `ids-comm.jar` as a dependency to your Java project. The rest of this page describes how to start a server and how to create a client for for the Trusted Connector.


#### Starting the Server

The server is started by creating an `IdscpServer` object and registering a `SocketListener` that will react to incoming messages.

```java
IdscpServer server =
  new IdscpServer()
   .setSocketListener(listener)
   .start();
```

In this simple example, the server will listen on port 8080, use the attestation type `BASIC`, the TLS private key from the default keystore and will not contact any third party to verify the trustworthiness of the remote party, i.e. the remote attestation will never succeed.

You may want to adapt the configuration:

``` java
IdscpServer server =
  new IdscpServer()
    .config(
       new ServerConfiguration.Builder()
          .port(8081)  // Set port to 8081
          .attestationType(IdsAttestationType.BASIC)  // Use BASIC attestation
          .setKeyStore(ks)  // Use private key from a specific key store
          .ttpUrl(new URI("https://my.remote.party/ttp"))  // Use specific trusted third party for remote attestation
          .build())
   .setSocketListener(listener)
   .start();
```
#### Starting the Client

Starting the client is simple. Just create a new `IdscpClient` object and connect to the server's hostname and port.

``` java
IdscpClient client = new IdscpClient();
WebSocket wsClient = client.connect("localhost", 8081);
```

The client will use the standard Java truststore to validate the server's TLS certificate chain. That is, all certificates along the trust chain must be installed in the truststore, otherwise you will get an `PKIX path building failed` error or similar.

Alternatively, the server's certificate can be _pinned_ in the client. In this case, the truststore is not used, but rather the hash of the server certificate is compared with a hardcoded value set by the `setSha256CertificateHashes()` configuration. This method is recommended if the server is known in advance and hardcoding its certificate in the client is a viable approach (i.e., in case it will not change or there is a process in place to update the pinned certificate in the client).


``` bash
IdscpClient client = new IdscpClient().config(
    new ClientConfiguration.Builder()
      .setSha256CertificateHashes(Collections.singletonList(
          DatatypeConverter.parseHexBinary(
          "4439DA49F320E3786319A5CF8D69F3A0831C4801B5CE3A14570EA84E0ECD82B0")))
     .build());
WebSocket wsClient = client.connect("localhost", 8081);
```


#### Checking Attestation Results

The IDSCP will run a mutual remote attestation between client and server to verify the integrity and trustworthiness of the software stack of the remote party.

An IDSCP session will always be established and client and server can exchange payloads independent of the attestation results. Developers are however advised to check the result of the remote attestation and closing the session in case of a failed attestation to avoid communication with untrusted parties.

The following snippet checks the remote attestation result and closes the WebSocket if it was not successful. When closing the session, it is recommend to use status code 1008 as defined in [RFC6455](https://tools.ietf.org/html/rfc6455#section-7.4.1) to indicate that the policy was violated.


```java
RatResult attestationResult = client.getAttestationResult();
if (attestationResult.getStatus().equals(RatResult.Status.FAILED)) {
   wsclient.sendCloseFrame(1008, attestationResult.getReason());
}
```
