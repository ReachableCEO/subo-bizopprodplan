# Tooling Task List

## Add architecture-specific directories to file tree

### Various cross-compiler parts

-----

From <https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm>, you can find the following:

### GNU C/C++ Compiler

You can find the sources to Arm Embedded GCC at [git://gcc.gnu.org/git/gcc.git](git://gcc.gnu.org/git/gcc.git). All contributions are made to trunk. Patches are cherry-picked as needed to the Arm embedded branches.

### Binutils  

You can find the sources to Arm Embedded Binutils at [git://sourceware.org/git/binutils-gdb.git](git://sourceware.org/git/binutils-gdb.git). All embedded branches are under [users/ARM/embedded-binutils-[version]-branch](users/ARM/embedded-binutils-[version]-branch). Contribution is similar to GCC.

### GDB

You can find the sources to Arm Embedded Binutils at [users/ARM/embedded-binutils-[version]-branch](git://sourceware.org/git/binutils-gdb.git). All embedded branches are at [users/ARM/embedded-gdb-[version]-branch](users/ARM/embedded-gdb-[version]-branch). You can contribute in the same way that you contribute to GCC.

### Newlib

You can find the sources at [users/ARM/embedded-gdb-[version]-branch](users/ARM/embedded-gdb-[version]-branch). We contribute to master, and the toolchain is based on master branch.

-----

### crosstool-NG [http://crosstool-ng.org/#download_and_usage](http://crosstool-ng.org/#download_and_usage)

x86 install (buildbox)

### Arduino-cli

x86 - RPi cluster install (into /subo/arm directory), deploying Uno and Mega Arduinos)

### Cross-compilers

x86-to-ARM cross-compiler (gcc, binutils, gdb), deploying RPi

x86-to-M0 cross-compiler (gcc, binutils, gdb), deploying M0

x86-to-AVR cross-compiler (gcc, etc.?), deploying Uno, Mega

### CUDA / JetPack (low priority)

suboprod  
jetson nano  
suboprod[n] - 01 through 05

### Configure Jenkins

-----

## Avionics - Seeeduino

### Install FreeRTOS

### IMU - Interface with

Multiple i2c  
IMU  
Functions - ReadPitch, ReadYaw, ReadRoll, ReadTime, ReadTemp, etc.

### Parafoil Control

Motor driver board drivers  
Functions - OrbiterOperateParafoil (motor left, right, up, down), OrbiterCutDown, EnvelopePop, DeployParafoil, OrbiterSelfDestruct

### Power Management

Functions - ReadVoltage, ReadCurrent, ReadAmps, ReadChargeLevel, etc.

### Telemetry

Key data - Charge levels, Full IMU data, Full sensor data (light, temp, etc.)  
Broadcast to ground stations (via lora), other   aircraft (via ads-b)  
Functions  

> Future, no ads-b yet, ADS-B(send), ADS-B(receive)  
>  
> Lora(send), lora(receive), IMU Sensor Read, IMU
Sensor Send, Data Logging, GPS Read, GPS Send, Charge Status, Battery Voltage  

### Overhead / Housekeeping

Functions - TickleTheTail

## Avionics - Raspberry Pi

Payload Power Management

### Functions - Payload Power On, Payload PowerOff, Payload Location Lookup

### Geo Fencing obtain tile38 data

### Operating System - Balena OS, Openmtc

### Avionics Updating will use Arduino-cli

## Ground Station Avionics

### Receive lora broadcasts

### Convert lora data to IP packets, push packets to a bus

### Set up chirpserver

### Set up openmtc
