# TSYS Group Handbook - Engineering Documentation - Team SwEng - Tooling


## Introduction

This article covers the tools and support stack/systems used by the software engineering team at Suborbital Systems. It should be read by everyone in the engineering organization.

## Sw Engineering Systems

* Build/dev/test systems

  * buildbox (All compiles happen here (either directly or via jenkins invoking)
  * subodev / suboqa (All dev/testing is done on these systems, in particular for hardware engineering test suite iteration etc)

* Production systems

  * suboprod (All production workloads, validations, simulations run here, cuda enabled for speed. May use docker in future to allow easy sharing of cuda card and versioning workloads)
  * suboprod01-05 (cuda tk1 cluster)
  * opc01/02 (op center systems)

## General Information

* All systems have NTP sync time, and working DNS
* All systems have an MOTD covering the basics and pointers to detailed documentation
* All systems have /subo mounted via NFS

### /subo directory

    localuser@buildbox:~$ ls /subo/
    SupplyChain Tooling 
    localuser@buildbox:~$ 

* **SupplyChain** is for upstream software that is used in deliverables produced by engineering. It would also contain any libraries/custom software etc that engineering produces

* **Tooling** is for off the shelf , upstream, vendor software that is used to support engineering operations. Things like (cross) compilers, custom builds of software etc go in it.

Each directory has the same top-level layout:

```bash
SupplyChain:
bin  lib  scripts  src

Tooling:
bin  lib  scripts  src
```

* bin is for installed packages (either stand alone (go/java) executables , or compiled software distributions) (with sub directories off of bin for each package, even if it's a single executable (we like clean systems and toolboxes))

```bash
root@buildbox:/subo/Tooling/bin# ls team-sweng/
arduino-cli

root@buildbox:/subo/Tooling/bin# ls team-sweng/arduino-cli/
arduino-cli
root@buildbox:/subo/Tooling/bin#
```

This is then referenced in /etc/profile as part of the path:

```bash

    export PATH="/subo/Tooling/bin/team-mecheng/openvsp/OpenVSP-3.23.0-Linux:\
    /subo/Tooling/lib/miniconda/bin:\
    /subo/Tooling/bin/team-sweng/arduino-cli:\
    $PATH"
    

    localuser@buildbox:~$ echo $PATH 
    /subo/Tooling/bin/team-mecheng/openvsp/OpenVSP-3.23.0-Linux:/subo/Tooling/lib/miniconda/bin:/subo/Tooling/bin/team-mecheng/openvsp/OpenVSP-3.23.0-Linux:/subo/Tooling/lib/miniconda/bin:/subo/Tooling/bin/team-sweng/arduino-cli:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    localuser@buildbox:~$
```

We will probably create a /subo/bin directory and symlink things to it and add that to the path (if/when the $PATH environment variable becomes too long)

The path in /etc/profile must be updated on subodev/qa/prod when new software is built on buildbox (and installed to /subo/..../bin)

* lib is for any non apt-get installed libraries needed for anything we are building from source

* src is for the (typically) git clone/checkout of upstream software, with a sub directory for each package, just like bin.

* scripts is for scripts (see immediately below for two key scripts)

### Dependency tracking

```bash
root@buildbox:/subo/Tooling/scripts# ls
install-build-deps.sh  install-runtime-deps.sh
root@buildbox:/subo/Tooling/scripts# 
```

* Tracking Build Dependencies

If you apt-get a package/packages to make cmake/configure work, capture it in
**install-build-deps.sh**

* Tracking Runtime Dependencies
If you apt-get a package/packages to make cmake/configure work, capture it in
**install-runtime-deps.sh**

## Cross Compilers

### Cross compile for pi and make custom images

* <http://rpi-cloud.com/guide-install-jenkins-on-rpi/>
* <http://software-novotny.de/raspberry-pi-remote-compilation-with-jenkins>
* <http://watchmysys.com/blog/tag/cross-compile/>
* <https://github.com/andrius/build-raspbian-image>
* <https://github.com/debian-pi/raspbian-ua-netinst>

* atmel (arduino mega/uno)

  * Documentation: <https://medium.com/swlh/how-to-create-an-automated-build-pipeline-for-your-arduino-project-1df9826f2a5e>

  * Location on buildbox:

```bash
/subo/Tooling/src/team-sweng/cross-compile/rpi
```
  

* pi
  * Documentation: <https://www.raspberrypi.org/documentation/linux/kernel/building.md> and
<https://blog.kitware.com/cross-compiling-for-raspberry-pi/>

  * Location on buildbox:

```bash
/subo/Tooling/src/team-sweng/cross-compile/rpi
```

which contains the current checkout (as of 03/02/2021)

* cortex (m0)

  * Documentation:  <https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm> 

  * Location on buildbox:

```bash

 /subo/Tooling/src/team-sweng/cross-compile/cortexm0

 root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/cortexm0 ls
 binutils-gdb  gcc  newlib-cygwin
 root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/cortexm0 
```

* crosstool-ng

  * Documentation: <http://crosstool-ng.github.io/docs/install/>
 
  * Location on buildobx: 

```bash
 /subo/Tooling/src/team-sweng/cross-compile/crosstool-ng
```

 which may well prove easier/better than the raw cloned sources enumerated in the last bullet point.

* nvidia

We will be making extensive use of Nvidia systems at subo, especially in R&d (for engineering/validation etc)

  * Documentation: * <https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/> and * <https://elinux.org/Jetson/Installing_CUDA>

Also see below under Software Development Kits , the CUDA bullet.

## Regular Compilers

buildbox is Ubuntu 20.04 and has bison/flex/autoconf/gcc/g++/build-essential (etc etc) via apt-get install as of 03/02/2021. run apt-get update/upgrade at any time (since anything regulatory scope, we build/install/version lock in /subo/....)

## Meta tooling

* distcc ?
* ccache ?
* ninja make?

## Software Development Kits

### CUDA / JetPack

  * <https://developer.nvidia.com/cuda-downloads>
  * <https://developer.nvidia.com/embedded/jetpack>

So this will be a bit tricky. We have three CUDA targets:

1 suboprod vm  (hosted on pfv-vmsrv-02)

```bash
root@pfv-vmsrv-02:~# lspci |grep -i nv
01:00.0 VGA compatible controller: NVIDIA Corporation GF104GLM [Quadro 3000M] (rev a1)
```

2 jetson nano (<https://developer.nvidia.com/embedded/jetson-nano-developer-kit>) (this might? use cuda)

3 suboprod01-05 (tk1) (kepler)

* <https://developer.nvidia.com/blog/jetson-tk1-mobile-embedded-supercomputer-cuda-everywhere/>
   (this uses cuda actual)
* <https://developer.nvidia.com/embedded-computing>
* <https://developer.nvidia.com/embedded/jetson-developer-kits>
* <http://mercury.pr.erau.edu/~siewerts/cec450/documents/Jetson/Tegra_Linux_Driver_Package_Developers_Guide.pdf>

Charles may tackle this, either way it's a (for now) low priority as far as tooling goes for
software engineering tooling. On the other hand, hardware/mechanical engineering will have much greater need for this stack for simulations/validation etc (for example <https://developer.nvidia.com/blog/drop-in-acceleration-gnu-octave/>).

### Areas to explore

* CUDA clustering
* mixed gpu gens/archs

### Links

* <https://www.google.com/search?client=firefox-b-e&q=ubuntu+20.04+cuda+cross+compile&oq=ubuntu+20.04+cuda+cross+compile&aqs=heirloom-srp>..

* <https://www.google.com/search?q=cuda+jenkins&oq=cuda+jenkins&aqs=chrome..69i57.2296j0j7&sourceid=chrome&ie=UTF-8>

* <https://www.google.com/search?client=firefox-b-e&q=cuda+build+server>
* <https://www.google.com/search?client=firefox-b-e&ei=cFxIYKzcDJaStAaMsKrICA&q=cuda+ci+server&oq=cuda+ci+server&gs_lcp=Cgdnd3Mtd2l6EAM6BwgAEEcQsANQ3idYmShg3SloAnACeACAAYYDiAH6BJIBBzAuMi4wLjGYAQCgAQGqAQdnd3Mtd2l6yAEIuAECwAEB&sclient=gws-wiz&ved=0ahUKEwis1PyjgqXvAhUWCc0KHQyYCokQ4dUDCA4&uact=5>
* <https://www.vitaarca.net/post/tech/install-cuda-and-cudnn-to-ubuntu-server/>
* <https://stackoverflow.com/questions/63309619/is-it-possible-to-build-an-nvidia-cuda-based-image-on-a-server-without-a-gpu>
* <https://jupyterhub.readthedocs.io/en/latest/>
* <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html>
* <https://crosstool-ng.github.io/docs/>

