---
layout: doc
title: Embedding the IDS protocol
permalink: /docs/idscp2/

---

Trusted Connectors use the _IDS Communication Protocol 2_ (IDSCP2) to exchange data over a secure channel. It is a TLS/TCP/IP-based protocol that establishes a bidirectional session between connectors and allows to send any payloads. The payload format is not defined by IDSCP2, so it can be used as a mere transport layer, leaving it up to the application to send XML, Protobuf, or any other binary payload. Instead of using a full-blown Connector, the protocol can also be used by standalone applications either acting as server or as client.

Using it requires adding the artifact `de.fhg.aisec.ids:idscp2` as a dependency to your Java project. Usage examples for the protocol, acting as client and server, can be found in the subproject `idscp2-examples` on the corresponding GitHub repository: https://github.com/industrial-data-space/idscp2-java

If you want to communicate with the `camel-idscp2` Apache Camel component, found in artifact `de.fhg.aisec.ids:camel-idscp2`, you will have to use `de.fhg.aisec.ids:idscp2-app-layer` instead. This artifact defines a very simplistic "application layer" on top of plain IDSCP2, which defines both "generic" and "IDS-conform" messages, both consisting of a (UTF-8-encoded) string header and a binary payload section. The camel component supports both kinds of messages. The only difference is that, whenever IDS-style messages are used, the Camel component expects the header to contain JSON-LD data describing a IDSMessage according to the IAIS Information Model, see https://github.com/International-Data-Spaces-Association/InformationModel.
