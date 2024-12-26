# TSYS Group Handbook - Engineering Documentation - Team HwEng - Supply Chain

## Introduction

This article covers the high level overview of the hardware supply chain for all aspects of
the orbiter:

* orbiter envelope
* orbiter parafoil

* orbiter part 101 requirements
  * orbiter cutdown
  * ads

* orbiter avionics
  * arduino
  * pi

* ground station hardware for receiving avionics data

## Pi Systems and USB accessories

* raspberry pi
  * subopi1
    * MorsePOD M6
  * subopi2
    * Lora Concentrator Pi Hat (Rak 833, 915mhz, SPI, FCC: 2AF6B-RAK833)
    * Arduino Uno connected to motor shield connected to
      * Servo
  * subopi3
    * USB Lora Concentrator
  * subopi4
    * Dragino Lora/GPS hat
  * subopi5
    * SenseHAT
    * Seeduino LorawanGPS connected to
      * IMU
      * Motor Controller
  * subopi6
    * SenseHAT
    * Seeduino LorawanGPS connected to
      * IMU
      * Motor Controller

## Orbiter Envelope

Much to write here, very soon.

## Orbiter parafoil

Much to write here, very soon.

## Orbiter Part 101 requirements

### Reflector

Much to write here, very soon.

### Redundancy

Much to write here, very soon.

### Orbiter Cutdowon

Much to write here, very soon.

## Orbiter Avionics - Seeduino

This is the core of the "smarts" (electronic) system for the orbiter. It's where all domain awareness and decision making happens (along with communication), and is subject to full regulatory review/compliance requirements.

It must do very few things, and it must do them perfectly.

It will be running FreeRTOS, with a handful of custom routines.

* The POC utilizes Seedunio LoraWANGPS boards:

  * <https://www.seeedstudio.com/Seeeduino-LoRaWAN-W-GPS-p-2781.html>

* With the following groove boards connected to the seeduino:

  * IMU: <https://www.seeedstudio.com/Grove-IMU-10DOF-p-2386.html>

  * Motor driver board: <https://www.seeedstudio.com/Grove-I2C-Motor-Driver-L298P-p-4534.html> (or very similar , need to confirm)

  * Motors (need to document)

### Power Management

* Core power control:
This is where the batteries, cells, load all comes together:

  * <http://www.switchdoc.com/sunairplus-solar-power-controllerdata-collector/>

### Hardware Watchdog

This is a critical fail safe component, and will reboot the seeduino or pi automatically if countdown timer expires:

* <https://shop.switchdoc.com/collections/break-out-boards/products/switchdoc-labs-dual-watchdog-timer-board-for-arduino-raspberry-pi>

## Orbiter Avionics - Raspberry pi

The pi is essentially a large disk (for geospatial db, holding avionics firmware, holding log data etc) and beefy CPU (for geo spatial lookups, processing ads-b data, perhaps some data crunching for local optimizations based on weather) attached to the avionics. The pi is important, but not completely critical. It can crash (and if not fixed by watchdog) can remain offline and the orbiter can come in for service safely. We will utilize best practices with the pi, to ensure it's as reliable/available as possible.

In poc the pi will be attached via USB (and also serving as a host for the arduino for firware updates of the avionics ), in prototype/prod it will be on an integrated board as a Pi CM.

* Payload power control:

Control of the power payload, on a geo spatial basis, in a black box/non customer changeable is also super critical, and will likely receive the second most regulatory scrutiny.

This is used for power cycling the payload for regulatory compliance (geofence):

* <https://shop.switchdoc.com/collections/break-out-boards/products/usb-powercontrol-board-v2-w-grove-control-usb-to-usb-solid-state-relay-for-raspberry-pi-and-arduinos-v2>

* SenseHat

We have a SenseHat on the raspberry pi, to provide additional sensor data, as a second source of data for comparsion purposes/redundancy.

## Ground Station

From a hardware perspective, not too much involved here. The lora concentrators we have
for modulating/demodulating lora packets are:

(attached via USB)

* <https://github.com/RAKWireless>
* <https://github.com/RAKWireless/RAK831-LoRaGateway-RPi>
* <https://store.rakwireless.com/products/rak831-gateway-module?variant=22375114801252>

and

(pi hat)
