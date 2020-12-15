---
layout: doc
title: Embedding the IDS protocol
permalink: /docs/idscp/

---

Trusted Connectors use the _IDS Communication Protocol_ to exchange data over a secure channel. It is a TLS/WebSocket-based protocol that establishes a bidirectional session between connectors and allows to send any payloads. The payload format is not defined by IDSCP so it can be used as a mere transport layer, leaving it up to the application to send seralized messages in XML, Protobuf, JMS, MQTT, OPC-UA, or any other OSI-layer 7 message format. Instead of using a full-blown Connector, the protocol can also be used by standalone applications either acting as server or as client.

This requires adding the `ids-comm.jar` as a dependency to your Java project. The rest of this page describes how to start a server and how to create a client for for the Trusted Connector.


##### Starting the Server

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
