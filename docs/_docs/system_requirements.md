---
layout: doc
title: System requirements
permalink: /docs/system_requirements/

---

System requirements of the Trusted Connector vary, depending on the chosen container management layer and the services running in the Core Platform. The following requirements should be considered as reasonable minimum requirements.

The Trusted Connector is designed to have a small minimum footprint, while at the same time supporting large-scale deployments in the cloud .

##### Supported Architectures

* x86 64 bit is fully supported with trustme and Docker stacks.
* ARM Cortex is supported with trustme and Docker stacks. TPM integration might need extra cross-compilation
* PowerPC is experimental


##### Storage

* Core Platform container: ~100 MB, plus
    * trustme container management layer: ~50 MB
    * Docker container management layer: depending on the underlying OS.
    A minimal Raspbian Jessie Lite for the Raspberry Pi is ~ 300MB, a minimum CoreOS is ~280 MB
 * At runtime additional ~100 MB should be available

##### Memory

* A minimum of 128 MB memory is required
* A minimum of 512 MB memory is recommended
* Depending on the applications and message queues running in the Trusted Connector, >1 GB memory is reasonable
