---
layout: doc
title: Using IDSCPv2
permalink: /docs/using_idscpv2/

---
As stated in the section before, Trusted Connectors use the _IDS Communication Protocol_ to exchange data over a secure channel. It is a TLS/WebSocket-based protocol that establishes a bidirectional session between connectors and allows to send any payloads.
In IDSCPv2, a full out-of-the-box bidirectional message exchange is supported. IDS Message types are supported as well for client/server interaction.

##### IDSCPv2 message types
Introducing message types in IDSCPv2, different message types are not supported that allow for integration into generic IDS communication patterns. The message type is held within a header field called 'idscp2.type'.

##### Using IDSCPv2 in a Camel route
The intrinsic bidirectional usage of IDSCPv2 allows for new communication patterns, but also calls for new route definitions.

##### Client to server communication
###### Using IDSCPv2 as a passive (receiving) server component

To set up a server with IDSCPv2, a route is created to listen for incoming connections on the specified port (29292) and a arbitrary IP address. These connections will be forwarded to the idscpv2server Camel component that will process the request.

In this example, whenever a message is transmitted to the defined endpoint, a automated reply is generated ('PONG') and the message type is set to the type 'pong'.

```xml
<route id="idscp2Server">
  <from uri="idscp2server://0.0.0.0:29292?sslContextParameters=#serverSslContext"/>
  <log message="Server received: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
  <setBody>
      <simple>PONG</simple>
  </setBody>
  <setHeader name="idscp2.type">
      <simple>pong</simple>
  </setHeader>
  <log message="Server response: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
</route>
```

###### Using IDSCPv2 as a sending client component

To set up IDSCPv2 as a client component in true bidirectional mode, two routes need to be created. The first route generates a message with content 'PING' and message type 'ping'. the second one processes the response from the server component.

```xml
<route id="idscp2SenderClient">
    <from uri="timer://tenSecondsTimer?fixedRate=true&amp;period=10000"/>
    <setBody>
        <simple>PING</simple>
    </setBody>
    <setHeader name="idscp2.type">
        <simple>ping</simple>
    </setHeader>
    <log message="Client sends: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
    <to uri="idscp2client://localhost:29292?connectionShareId=pingPongConnection&amp;sslContextParameters=#clientSslContext"/>
</route>

<route id="idscp2ReceiverClient">
    <from uri="idscp2client://localhost:29292?connectionShareId=pingPongConnection&amp;sslContextParameters=#clientSslContext"/>
    <log message="Client received: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
    <!-- Prevents the client consumer from sending the message back to the server -->
    <removeHeader headerName="idscp2.type"/>
</route>
```

##### Server to client communication
###### Using IDSCPv2 as a active (sending) server component
To generate broadcast messages to all connected clients, is is possible to set up a server route locally. This way, a server that sends a message to its own endpoint will broadcast this message to all connected clients.

```xml
<route id="idscp2Server">
    <from uri="timer://tenSecondsTimer?fixedRate=true&amp;period=10000"/>
    <setBody>
        <simple>BROADCAST</simple>
    </setBody>
    <setHeader name="idscp2.type">
        <simple>broadcast</simple>
    </setHeader>
    <log message="Server broadcast to connected clients: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
    <to uri="idscp2server://0.0.0.0:29292?sslContextParameters=#serverSslContext"/>
</route>
```
###### Using IDSCPv2 as a passive (receiving) client component
To set up a client to receive message from the server, it can be set up as the first route hop. All received server messages will be processed further on.

```xml
<route id="idscp2ReceiverClient">
    <from uri="idscp2client://localhost:29292?sslContextParameters=#clientSslContext"/>
    <log message="Client received: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
    <!-- Prevents the client consumer from sending the message back to the server -->
    <removeHeader headerName="idscp2.type"/>
</route>
```
