---
layout: doc
title: Build trustme OS
permalink: /docs/dev_setup/

---

The Trusted Connector supports two container management solutions:

* Docker, the well-established cloud-ready container management system
* trustme OS, a highly secure container solution for embedded and mobile systems.

trustme features verification and strong container-based isolation of apps, trusted boot, and supports remote attestation of Trusted Connectors. Its source code is available under a [GPLv2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html) license.

<div style="text-align:center">
	<img src="../../assets/img/trustme.png"/>
</div>


trustme currently comes with support for ARM and x86 platforms and can be built in different flavors:

* Android Lollipop 5.1.1_r38 for the Google Nexus 5 (hammerhead) device (ARM)
* Android Nougat 7.0.0_r6 for the Google Nexus 5 (hammerhead) device (ARM)
* __IDS Trusted Connector (x86)__


To build trustme for the __Trusted Connector on x86 platforms__ from source, follow the guidelines at [https://github.com/trustm3/](https://github.com/trustm3/trustme_build/blob/trustme-5.1.1_r38-github/doc/ids-README.md).

We recommend using the docker-based build environment:

1. Checkout the build project
    ```
    git clone git@github.com:trustm3/trustme_build.git
    ```
1. Create build environment as a docker container
   ```
   cd trustme_build
   docker build . -t trustme-builder
   ```
1. Run the build environment as a docker container. Assuming you checked out the sources of the trustme main project into `~/workspace/trustme`, mount them into the build environment as follow:
   ```
   docker run -ti --name trustme-builder --rm -v ~/workspace/trustme:/root/workspace trust-builder /bin/bash
   ```
 1. Within the docker container, start the build as described in the trustme documentation [https://github.com/trustm3/](https://github.com/trustm3/trustme_build/blob/trustme-5.1.1_r38-github/doc/ids-README.md):
     ```
     make ids-all
     ```
