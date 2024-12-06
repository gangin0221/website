---
title: Building webOS OSE
display_title: Building webOS Open Source Edition
date: 2024-12-02
weight: 20
toc: true
---

This page describes how to build a webOS Open Source Edition (OSE) image from source code.

## Before You Begin

- Make sure that your system meets the [Build System Requirements]({{< relref "system-requirements#build-system-requirements" >}}).
- Basic knowledge about how to use [Git](https://git-scm.com/) is required.

{{< note >}}
Building webOS OSE image requires **Ubuntu** and **significant computing resources** (See [Appendix B. Build Time Test](#appendix-b-build-time-test)). If building the image locally is not feasible, consider using [pre-built images](https://github.com/webosose/build-webos/releases).
{{< /note >}}

## Quick Summary

Here is a quick summary for users already familiar with building webOS OSE. If you are new to webOS OSE, we recommend reading the whole document thoroughly.

```bash
# Download source codes
$ git clone https://github.com/webosose/build-webos.git
$ cd build-webos

# Install and configure the build
$ sudo scripts/prerequisites.sh
$ ./mcf -p <half the num of CPUs> -b <half the num of CPUs> <image type>

# Start to build
$ source oe-init-build-env
$ bitbake webos-image
```

{{< note >}}
If you have any problem during the build process, please check the [Troubleshooting Guide](#troubleshooting-guide) section.
{{< /note >}}

## Cloning the Repository

You can start the webOS OSE build by cloning the [build-webos repository](https://github.com/webosose/build-webos). 

```bash
$ git clone https://github.com/webosose/build-webos.git
$ cd build-webos
```

### (Optional) How to Handle Patch Versions

Since webOS OSE consists of many open-source projects, changes in those projects can sometimes cause unexpected build-time errors. To address this issue, we introduced a new branch policy starting with the [webOS OSE 2.19.1 release]({{< relref "2022-12-29-webos-ose-2-19-1-release" >}}). This new policy enables the platform to quickly adapt to critical changes.

All you need to do is to **check the patch number of the latest version**.

- If the patch number is not zero, switch to the designated branch before starting the build. 
- Otherwise, use the `master` branch.

``` bash
# If the patch number of the latest version is not 0, checkout to the latest branch
# else, use the master branch
# Example) If the latest version is 2.19.1 (non-zero patch number)
$ git checkout 2.19.1
```

{{< figure src="/images/blog/news/webos-ose-versioning-rule.jpg" width="60%" caption="Versioning rule of webOS OSE" >}}

## Installing the Required Tools and Libraries

During the building process, [BitBake](https://docs.yoctoproject.org/bitbake.html) might fail a sanity check. Although BitBake tells you what is missing, it doesn't install the missing tools and libraries. 

You can force to install all of the missing software by entering the following:

```bash
$ sudo scripts/prerequisites.sh
```

## Configuring the Build

Using the `mcf` command, you can set up the followings:

- The **Type** of webOS OSE image to build
- The **number of logical CPU cores** to allocate to the build process

```bash
$ ./mcf -p <num of CPUs> -b <num of CPUs> <image type>
```

| Property | Description |
|----------|-------------|
| `<num of CPUs>` | This number determines how many CPU cores are allocated for the building process. We recommend using **50 to 70%** of the total logical CPU cores. See [Appendix A. Setting Values for mcf](#appendix-a-setting-values-for-mcf).|
| `<image type>` | A type of the webOS OSE image. Available values are as follows: <ul><li><code>raspberrypi4</code>: 32-bit image for webOS OSE 2.0.0 or higher</li><li><code>raspberrypi4-64</code>: 64-bit image for webOS OSE 2.0.0 or higher</li><li><code>raspberrypi3</code>: 32-bit image for webOS OSE 1.x version</li><li><code>raspberrypi3-64</code>: 64-bit image for webOS OSE 1.x version</li><li><code>qemux86</code>: 32-bit image for webOS OSE emulator</li><li><code>qemux86-64</code>: 64-bit image for webOS OSE emulator (For webOS OSE 2.14.0 or higher)</li></ul> {{< caution >}}
Previous versions of webOS OSE might occur errors during build time. We only guarantee the build of the latest version.
{{< /caution >}} |

## Building the Image

webOS OSE provides two types of images:

- `webos-image`: A standard webOS OSE image
- `webos-image-devel`: A webOS OSE image with various development tools such as [GDB](https://www.sourceware.org/gdb/) and [strace](https://strace.io/) (system call tracer)

### Building webos-image

{{< note "IMPORTANT NOTICE" >}}
This process takes a very long time, especially on laptop computers. Make sure you have enough time and system resources to build. Regarding the build time, refer to [Appendix B. Build Time Test](#appendix-b-build-time-test).
{{< /note >}}

To kick off a full build of webOS OSE, enter the following:

```bash
$ source oe-init-build-env
$ bitbake webos-image
```

Alternatively, you can enter:

```bash
$ make webos-image
```

### Building webos-image-devel

```bash
$ source oe-init-build-env
$ bitbake webos-image-devel
```

{{< note >}}
* For details on setting up the environment to debug webOS OSE with GDB, see [GDB Debugging Setup]({{< relref "setting-up-debugging" >}}).
* For more information on how to use strace, refer to [the article on strace](https://www.thegeekstuff.com/2011/11/strace-examples/).
{{< /note >}}

### Checking the Built Image

To check if the image has been built successfully, check the following directories:

* For Raspberry Pi 4, the resulting image will be created at
  * 32-bit: `BUILD/deploy/images/raspberrypi4/webos-image-raspberrypi4.rootfs.wic.bz2`.
  * 64-bit: `BUILD/deploy/images/raspberrypi4-64/webos-image-raspberrypi4-64.rootfs.wic.bz2`.
* For Raspberry Pi 3, the resulting image will be created at
  * 32-bit: `BUILD/deploy/images/raspberrypi3/webos-image-raspberrypi3.rootfs.rpi-sdimg`.
  * 64-bit: `BUILD/deploy/images/raspberrypi3-64/webos-image-raspberrypi3-64.rootfs.rpi-sdimg`.
* For the emulator, the resulting image will be created at
  * 32-bit: `BUILD/deploy/images/qemux86/webos-image-qemux86-master-*-wic.vmdk.gz`.
  * 64-bit: `BUILD/deploy/images/qemux86-64/webos-image-qemux86-64-master-*-wic.vmdk.gz`.

If the built image exists, move on to the [Next Steps]({{< relref "#next-steps" >}}).

## Cleaning

To blow away the build artifacts and prepare to do the clean build, you can remove the build directory and recreate it by typing:

```bash
$ rm -rf BUILD
$ ./mcf.status
```

This retains the caches of the downloaded source (under `build-webos/downloads`) and shared state (under `build-webos/sstate-cache`). These caches will save you a tremendous amount of time during development as they facilitate incremental builds, but these also can cause seemingly inexplicable behavior when corrupted. 

For more details, see [Yocto Project Overview and Concepts Manual](https://docs.yoctoproject.org/overview-manual/concepts.html#shared-state-cache).

## Building and Cleaning Individual Components

To build an individual component, enter:

```bash
$ source oe-init-build-env
$ bitbake <component-name>
```

Alternatively, you can enter:

```bash
$ make <component-name>
```

To clean a component's build artifacts under `BUILD`, enter:

```bash
$ source oe-init-build-env
$ bitbake -c clean <component-name>
```

To remove the shared state for a component as well as its build artifacts to ensure it gets rebuilt afresh from its source, enter:

```bash
$ source oe-init-build-env
$ bitbake -c cleansstate <component-name>
```

## Troubleshooting Guide

### Fetching Error During Build Process

webOS OSE fetches (downloads) many external modules during the build process. And these fetch jobs might fail occasionally.

In such case, **try cloning the failed module manually**. If the cloning succeeds, rerun the `bitbake` command to continue the build.

``` bash
# Try cloning the failed module
$ git clone <fetch error module>
# If the cloning succeeds, rerun the build process
$ bitbake webos-image
```

### Building Multiple Images On The Same Shell

If you try to build two (or more) different webOS OSE images on the same shell, this might cause a build error. To avoid such an error, do one of the following:

* Open a new shell and proceed from the [Building webos-image]({{< relref "building-webos-ose/#building-webos-image" >}}).
* Enter the following commands and proceed from the [Building webos-image]({{< relref "building-webos-ose/#building-webos-image" >}}).

    ``` bash
    $ unset DISTRO
    $ unset MACHINE
    $ unset MACHINES
    ```

## Next Steps

- If you built the image for Raspberry Pi 4 or Raspberry Pi 3, it's time to flash the image to the target device. See [Flashing webOS OSE]({{< relref "flashing-webos-ose" >}}).
- If you built the image for the emulator, refer to the [Emulator User Guide]({{< relref "emulator-user-guide" >}}) to set up and use the emulator.

## Appendix A. Setting Values for mcf

`-p` and `-b` options in `mcf` script are BitBake parallelism values, and those determine how many CPU cores are allocated for the building process.

Too many allocation for the parallelism values might cause build-time error. The recommended value is **50 to 70% of the toal logical CPU cores**. To get the number of your computer, enter the following command:

``` bash
# Get the number of logical CPU cores
$ cat /proc/cpuinfo | grep '^processor' | wc -l
```

{{< caution >}}
Omitting `-p` and `-b` options are equivalent to using `-p 0 -b 0`, which forces the build to use all CPU cores. This might cause unexpected behaviors or a build failure.
{{< /caution >}}

{{< note >}}
See `PARALLEL_MAKE` and `BB_NUMBER_THREADS` variables in [Yocto Project Development Tasks Manual](https://docs.yoctoproject.org/dev-manual/common-tasks.html#speeding-up-a-build).
{{< /note >}}

## Appendix B. Build Time Test

This section describes the actual build time of webOS OSE using our build machine.

Specifications of the test machine are as follows:

- CPU: Intel Xeon 6226R 2.9 GHz 2933 MHz 16C 150W
- RAM: 32 GB (16 GB x 2) DDR4 2933 DIMM ECC Registered 1CPI
- GPU: NVIDIA RTX A4000 16 GB FH Blower Fan 4DP PCle x16
- Storage: HP Z Turbo Drive M.2 2 TB TLC
- `<num of CPUs>` for `mcf` : 4

Test Results are as follows:

| Device Type | Image Type | Time |
|-------------|------------|------|
| `raspberrypi4-64` | `webos-image` | 8 hours 48 minutes |
| `raspberrypi4-64` | `webos-image-devel` | 8 hours 51 minutes |