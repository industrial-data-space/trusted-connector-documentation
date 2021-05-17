---
layout: doc
title: System requirements
permalink: /docs/system_requirements/

---

System requirements of the Trusted Connector vary, depending on the chosen container management layer and the services running in the Core Platform. The following requirements should be considered as reasonable minimum requirements.

The Trusted Connector is designed to have a small minimum footprint, while at the same time supporting large-scale deployments in the cloud.


##### Supported Architectures

The Trusted Connector supports x86 (amd64), PowerPC, and ARM architectures (including a Raspberry Pi docker image). The _Core Platform_ runs either standalone or interacts with a Docker or trustme container management layer.

It is tested for the following platforms:


<style>
table {
    display:table;
    width:70%;
}
table td {
	padding: 5px;
}
</style>

|           |  Platform | Description |
| --------- |------------------- | ---- |
| ![image-title-here](../../assets/img/tux_logo.png){: width="50px"} | Linux x86-64 | Tested with docker and trustme for x86|
| ![image-title-here](../../assets/img/android_logo.png){: width="50px"} | Android | Tested with trustme for ARM |
| ![image-title-here](../../assets/img/pi_logo.png){: width="50px"} | Raspberry Pi | Tested with docker image for Raspbian Strech (ARM)|
|  | 16/8-Core PowerPC | Tested with trustme, no Docker support |



##### Storage Requirements

* Core Platform container: ~100 MB, plus
    * ~50 MB for trustme container management layer, or
    * ~300 MB for Raspbian Lite Stretch with docker container management layer
 * At runtime additional ~100 MB should be available


##### Memory Requirements

* A minimum of 128 MB memory is required
* A minimum of 512 MB memory is recommended
* Depending on the applications and message queues running in the Trusted Connector, >1 GB memory is reasonable
