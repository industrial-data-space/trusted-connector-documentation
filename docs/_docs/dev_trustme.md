---
layout: doc
title: Build trustme OS
permalink: /docs/dev_trustme/

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

## Trustme platform

To build trustme for the __Trusted Connector on x86 platforms__ from source, follow the guidelines at [https://github.com/trustm3/](https://github.com/trustm3/trustme_build/blob/trustme-5.1.1_r38-github/doc/ids-README.md).

We recommend using the docker-based build environment:

1. Checkout the build project

	 ```
   git clone git@github.com:trustm3/trustme_build.git
   ```

1. Create build environment as a docker container, setting your mail address and name to configure the GIT environment:

	 ```
   cd trustme_build
   docker build --build-arg "GIT_EMAIL=your.name@yourdomain.com" --build-arg "GIT_NAME=Your Name" -t trustme-builder .
   ```

1. Run the build environment as a docker container. Assuming you checked out the sources of the trustme main project into `~/workspace/trustme`, mount them into the build environment as follows:

	 ```
   docker run -ti --name trustme-builder --rm -v ~/workspace/trustme:/root/workspace trustme-builder /bin/bash
	 ```

1. Within the docker container, start the build as described in the trustme documentation [https://github.com/trustm3/](https://github.com/trustm3/trustme_build/blob/trustme-5.1.1_r38-github/doc/ids-README.md):

	 ```
	 cd /root/workspace
	 make ids-all
   ```

1. When the build is complete, run trustme by using qemu:

   ```
	 qemu-system-x86_64 -kernel out-trustme/kernel/x86/obj/arch/x86/boot/bzImage -initrd out-cml/target/product/trustme_x86_cml/ramdisk.img -append "console=ttyS0 console_loglevel=7 debug selinux=0" -serial stdio -redir tcp:55550::55550 -redir tcp:8080::8080 -drive file=out-trustme/target/x86/userdata.img,format=raw,media=disk -nographic -m 1024M
   ```

1. First, export the adb file location to your environment:

   ```
   export PATH=$PATH:/root/workspace/out-cml/host/linux-x86/bin
   ```

1. Second, connect via adb

	 ```
   adb connect 127.0.0.1:55550
   adb root
	 adb connect 127.0.0.1:55550
   adb shell
	 ```

1. Exit the shell and deploy ids containers

	 ```
   make deploy_ids
	 adb shell reboot -f
   ```

1. Find the process id of the running container and connect to it:

   ```
   ps | grep cml-service-container
   cml-run PID /bin/bash --login
   ```


## Docker converter

Trustme and docker use a different image format. The main difference is than docker uses a stack of `OverlayFS` file system layers, supports various configurations and does not necessarily have to be signed. trustme mainly uses signed space-optimized `SquashFS` images and only provides the options required by either Android or the Trusted Connector.

The different image formats are not relevant to the end user. There is an converter tool which automatically converts docker into trustme images. It is thus possible to run any docker image on the trustme platform.

To build the converter tool, do the following within the build environment:

```
repo sync
source build/envsetup.sh
lunch
```

Select `trustme_x86_cml-userdebug`

```
m converter_host
```
