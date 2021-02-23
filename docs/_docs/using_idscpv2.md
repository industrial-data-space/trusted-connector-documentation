---
layout: doc
title: Using IDSCPv2
permalink: /docs/using_idscpv2/

---
As stated in the section before, Trusted Connectors use the _IDS Communication Protocol_ to exchange data over a secure channel. Speaking for IDSCPv1, it is a TLS/WebSocket-based protocol that establishes a bidirectional session between connectors and allowes to send any payloads.
In IDSCPv2, a full out-of-the-box bidirectional message exchange is supported and several modifications on the technical baseline are were performed. The protocol is now purely based on TCP/IP and protobuf. All dependencies to Websockets (and thus, the needed web stack, have been removed).  
IDS Message types are supported as well for client/server interaction which are explained in the following sections on a conceptual and technical level. 

## New Features Compared to IDSCPv1

* Bidirectional messages for client and server setup using Apache Camel
* IDS Message support
* Message Broadcast support
* Usage Control information support

## IDSCPv2 Message Types

Introducing message types in IDSCPv2, different message types are now supported that allow for integration into generic IDS communication patterns. The message type is held within a header field called 'idscp2.type'. The list of IDS message types that are specified by the [IDS Information Model Messages](https://htmlpreview.github.io/?https://github.com/IndustrialDataSpace/InformationModel/blob/feature/message_taxonomy_description/model/communication/Message_Description.htm) We are currently working on providing workflows which treat all the IDS Message types.

Currently the following IDS Message types are implemented using sample Camel Routes: 
* ArtifactRequestMessage
* ArtifactResponseMessage
* ContractRequestMessage
* ContractAgreementMessage
* ContractResponseMessage
* RejectionMessage
* ContractRejectionMessage

## Using IDSCPv2 in a Camel route
The intrinsic bidirectional usage of IDSCPv2 allows for new communication patterns, but also calls for new route definitions.

### Client to server communication
#### Using IDSCPv2 as a passive (receiving) server component

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
 
#### Using IDSCPv2 as a sending client component

To set up IDSCPv2 as a client component in true bidirectional mode, two routes need to be created. The first route generates a message with content 'PING' and message type 'ping'. the second one processes the response from the server component. The first <from> statement creates an empty message every 10s (period=10,000ms).

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

### Server to client communication
#### Using IDSCPv2 as a active (sending) server component
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


#### Using IDSCPv2 as a passive (receiving) client component
To set up a client to receive messages from the server, it can be set up as the first route hop. All received server messages will be processed further on.

```xml
<route id="idscp2ReceiverClient">
    <from uri="idscp2client://localhost:29292?sslContextParameters=#clientSslContext"/>
    <log message="Client received: ${body} (idscp2.type: ${headers[idscp2.type]})"/>
    <!-- Prevents the client consumer from sending the message back to the server -->
    <removeHeader headerName="idscp2.type"/>
</route>
```

### IDSCPv2 Sample Message Flow

The previous section detailed how an IDSCPv2 tunnel can be set up and handled between two connectors. However, the sample data which was exchanged as message body was just "PING" or "PONG" and no IDS Messages were used yet. This section explains the use of IDSCPv2 in combination with IDS Message flows.

#### Contract Agreement Message Flow

The following overview provides a simplified though typical IDS Contract Agreement phase.

<div style="text-align:center">
    <img width="500" src="../../assets/img/SequenceDiagramContractAgreement.svg"/>
</div>

The following Camel processors are involved and called when the corresponding message is received.

<div style="text-align:center">
    <img width="500" src="../../assets/img/SequenceDiagramContractAgreementProcessors.svg"/>
</div>

##### Initialisation on the Client Side

We trigger the message in our camel route on the client side using the ContractRequestCreationProcessor. 
NOTE: This part of the route can only be used if an idscp2client has already been initialised which means this route must be placed after a corresponding server route since Camel reads routes by sequence. 
Please see package "examples" and compare /route-examples/demo-route.xml" and the other sample routes in case of errors.

```xml
<route>
    <from uri="timer://foo?fixedRate=true&amp;period=30000" />
    <setBody><simple>${null}</simple></setBody>
    <log message="##### STARTING IDSCP2 CONTRACT-GIVEN MESSAGE FLOW #####"/>
    <setProperty name="artifactUri">
        <constant>https://example.com/some_artifact</constant>
    </setProperty>
    <process ref="ContractRequestCreationProcessor" />
    <to uri="idscp2client://localhost:29292?connectionShareId=pingPongConnection&amp;sslContextParameters=#sslContext&amp;useIdsMessages=true"/>
</route>
```

The processor reads out the artifactUri defined in the camel route and creates the following message request body and header as serialised JSON-LD. 
The message is sent using an IDSCPv2 tunnel from client to server as first step of the contract negotiation. 
The message itself is transmitted as HTTP multipart format with two separate objects, namely header and body.  
The serialization of the header of the message might look like this:

```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ContractRequestMessage",
  "@id" : "https://w3id.org/idsa/autogen/contractRequestMessage/321b5923-78d9-4d49-9b6e-b9b860a60ee6",
  "ids:modelVersion" : "4.0.0",
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:28.701+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/287a9524-2c67-41dd-b55f-08e95ad6188f",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
```

The serialization of the body of the message might look like this:
```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:ContractRequest",
  "@id" : "https://w3id.org/idsa/autogen/contractRequest/b3b711e4-a5ba-4076-9e44-144c50bfe68a",
  "ids:contractDate" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:contractEnd" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:contractStart" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:permission" : [ {
    "@type" : "ids:Permission",
    "@id" : "https://w3id.org/idsa/autogen/permission/471b980c-c7f5-4a1e-af39-e5993d7057ee",
    "ids:target" : {
      "@id" : "https://example.com/some_artifact"
    }
  } ]
}
```

##### Receiving and Handling Messages on the Server Side

The creation of the messages and the corresponding Camel message processors can be seen in the Trusted Connector github project in package "camel-idscp2".
We process the message on the server side with an additional Camel Route similar to this one. 
It basically consists of an IDSCPv2 server endpoint as explained in the previous sections, but this time additional message type checks are added and the corresponding Camel Processors are called.
When an unknown message type is received, its body is set to null to drop the message and stop endless looping as can be seen in the <otherwise> branch.
When an ContractAgreementMessage is received, the message flow is finished, therefore after its processing Camel strips its content as well as can be seen in the ContractAgreementMessage branch.
Note that there is no <to> branch used in the route due to the fact that this server endpoint automatically replies to the connected client peer once the end of the route is reached. 
Whenever the keyword <process> is used in a Camel Route, a subsequent defined Camel processor is called. 
The Camel Message Exchange object passes through the required processors and gets modified before the final result is sent back to the client side. 

```xml
<route id="server">
    <from uri="idscp2server://0.0.0.0:29292?sslContextParameters=#sslContext&amp;useIdsMessages=true"/>
    <process ref="TypeExtractionProcessor"/>
    <log message="### SERVER RECEIVER: Detected Message type: ${exchangeProperty.ids-type}"/>
    <choice>
        <!-- ContractRequestMessage -->
        <when>
            <simple>${exchangeProperty.ids-type} == 'ContractRequestMessage'</simple>
            <log message="### Handle ContractRequestMessage ###"/>
            <setProperty name="containerUri">
                <constant>https://hub.docker.com/layers/jmalloc/echo-server/latest/images/sha256-c461e7e54d947a8777413aaf9c624b4ad1f1bac5d8272475da859ae82c1abd7d#8080</constant>
            </setProperty>
            <process ref="ContractRequestProcessor"/>
        </when>
        <!-- ContractAgreementMessage -->
        <when>
            <simple>${exchangeProperty.ids-type} == 'ContractAgreementMessage'</simple>
            <log message="### Handle ContractAgreementMessage ###"/>
            <!-- Only processing of incoming request must be performed, no reply -->
            <process ref="ContractAgreementReceiverProcessor"/>
            <removeHeader headerName="idscp2-header" />
            <setBody><simple>${null}</simple></setBody>
        </when>
        <!-- ArtifactRequestMessage ... -->
        <when...>
        <!-- all undefined other messages -->
        <otherwise>
            <log message="### Server received (otherwise branch):\n${body}\n### Header: ###\n${headers[idscp2-header]}"/>
            <removeHeader headerName="idscp2-header" />
            <setBody><simple>${null}</simple></setBody>
        </otherwise>
    </choice>
</route>
```

The server replies with the following serialised Header: 
```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ContractAgreementMessage",
  "@id" : "https://w3id.org/idsa/autogen/contractAgreementMessage/bbb217e8-d946-45dc-891b-1e71b8071c60",
  "ids:modelVersion" : "4.0.0",
  "ids:correlationMessage" : {
    "@id" : "https://w3id.org/idsa/autogen/contractResponseMessage/ac7940f9-4707-44f1-853a-d2839cded1f1"
  },
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:36.328+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/9ccd5552-c556-49a3-9ff4-1fc89c8268dd",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
```

And body:

```json
Serialization body: {
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:ContractAgreement",
  "@id" : "https://w3id.org/idsa/autogen/contractAgreement/147884ed-70d6-4ec0-9739-15d7d376850f",
  "ids:permission" : [ {
    "@type" : "ids:Permission",
    "@id" : "https://w3id.org/idsa/autogen/permission/15c7c882-0f78-4ffc-a23b-94c05459a7f2",
    "ids:constraint" : [ {
      "@type" : "ids:Constraint",
      "@id" : "https://w3id.org/idsa/autogen/constraint/ce66908c-c05f-4559-b3f5-14b506175e07",
      "ids:leftOperand" : {
        "@id" : "idsc:SYSTEM"
      },
      "ids:operator" : {
        "@id" : "idsc:SAME_AS"
      },
      "ids:rightOperandReference" : {
        "@id" : "https://hub.docker.com/layers/fraunhoferaisec/trusted-connector-core/develop/images/sha256-aa9fe6d854fd01d0a098802bf76477fc66147646f11303e5c0c19a80add6406a#port=80"
      }
    } ],
    "ids:target" : {
      "@id" : "https://example.com/sample_artifact"
    }
  } ]
}
```

In this example the client requested a certain artifact without any constraints. 
The server processed the request and added an ids:constraint object to the message with the meaning of "idsc:SYSTEM" must be "idsc:SAME_AS" the docker container specified in the object "ids:rightOperandReference".
At this point usage control is configured since the system only allows data to be processed in a certain predefined and attested type of container.
More on Usage control can be seen in an extra documentation page.

##### Receiving and Handling Messages on the Client Side

The receiving mapping of IDS Messages to the corresponding Camel processors on the client side can be realised using the same pattern as for the server side.
This is the third required route (initialisation, server and clientReceiver) for our current message flow

```xml
<route id="clientReceiver">
    <from uri="idscp2client://localhost:29292?connectionShareId=pingPongConnection&amp;sslContextParameters=#sslContext&amp;useIdsMessages=true"/>
    <process ref="TypeExtractionProcessor"/>
    <log message="### CLIENT RECEIVER: Detected Message type: ${exchangeProperty.ids-type}"/>
    <choice>
        <!-- ContractResponseMessage -->
        <when>
            <simple>${exchangeProperty.ids-type} == 'ContractResponseMessage'</simple>
            <log message="### Handle ContractResponseMessage ###"/>
            <process ref="ContractResponseProcessor"/>
        </when>
        <!-- ArtifactResponseMessage -->
        <when...>
        <!-- all other undefined messages -->
        <otherwise>
            <log message="### Client received (otherwise branch):\n${body}\n### Header: ###\n${headers[idscp2-header]}"/>
            <removeHeader headerName="idscp2-header" />
            <setBody><simple>${null}</simple></setBody>
        </otherwise>
    </choice>
</route>    
```

Afterwards, if the client accepts the contract a ContractAgreementMessage is sent with the exact information given by the server as completion of the message flow.
If the client does not accept the proposed constraints from the server for a certain artifact a ContractRejectionMessage is sent.  

#### Artifact Request Message Flow

The following overview provides a simplified though typical IDS Artifact Request message flow.

<div style="text-align:center">
    <img width="500" src="../../assets/img/SequenceDiagramArtifactRequest.svg"/>
</div>

The following Camel processors are involved and called when the corresponding message is received.

<div style="text-align:center">
    <img width="500" src="../../assets/img/SequenceDiagramArtifactRequestProcessors.svg"/>
</div>

And messages similar to the following structure could be exchanged.

Client: ArtifactRequestMessage's serialised Header:
```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ArtifactRequestMessage",
  "@id" : "https://w3id.org/idsa/autogen/artifactRequestMessage/206e16cc-856b-48dd-bcc4-c6752e6ea3ce",
  "ids:modelVersion" : "4.0.0",
  "ids:requestedArtifact" : {
    "@id" : "https://example.com/some_artifact"
  },
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:41.267+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/4c1c1335-f98c-4d1e-bcd7-bfd143c656c1",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
```

Server: ArtifactResponseMessage's serialised Header:
```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ArtifactResponseMessage",
  "@id" : "https://w3id.org/idsa/autogen/artifactResponseMessage/6717d2ab-377e-4510-878a-cb4899a57b3a",
  "ids:modelVersion" : "4.0.0",
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:41.325+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/ae6e335a-0351-49f7-a438-f0e0e8a23d3b",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
```
with serialised Body:
```json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:Artifact",
  "@id" : "https://w3id.org/idsa/autogen/artifact/6f58407f-de3d-48a8-908e-2985977e3f51",
  "ids:fileName" : "testArtifactFilename.dat",
  "ids:byteSize" : 50000,
  "ids:creationDate" : {
    "@value" : "2020-11-17T10:42:41.341+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:checkSum" : "ABCDEFG-CHECKSUM",
  "ids:duration" : 5000
}
```
