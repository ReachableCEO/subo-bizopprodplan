# TSYS Group Handbook - Engineering Documentation - Team SwEng - Supply Chain

## Version

* 1.0 published 03/11/2021 at 08:30 CST

## Introduction

This article covers the high level overview of the software supply chain for the orbiter, ground station, operations center.

It touches very briefly on the hardware that will be used in the proof of concept. We have a separate article, that goes into far more detail about the hardware.

This article also doesn't touch on the software tooling (compilers and such), which is extensive and is documented in another article.

## Avionics - Seeduino

This is the core of the software system for the orbiter. It's where the magic happens, and is subject to full regulatory review/compliance requirements. It must do very few things, and it must do them perfectly.

This is all going to be custom code.

### Main board

* The proof of concept utilizes Seeduino LoraWANGPS boards:

 Overview: <https://www.seeedstudio.com/Seeeduino-LoRaWAN-W-GPS-p-2781.html>

 Technical Documentation: <https://wiki.seeedstudio.com/Seeeduino_LoRAWAN/> (unfortunately no ability to deep link on that page, so read it over for example code of Lora send/receive, battery checking etc)

* The boards will be running all code via FreeRTOS:

  * <https://www.freertos.org/FreeRTOS-quick-start-guide.html>
  * <https://wiki.seeedstudio.com/Software-FreeRTOS/>
  * <https://github.com/Seeed-Studio/Seeed_Arduino_FreeRTOS>
  * <https://github.com/Seeed-Studio/Seeed_Arduino_ooFreeRTOS>

FreeRTOS will need to interface with the following groove boards connected to the Seeduino via i2c:

(see <https://wiki.seeedstudio.com/Arduino_Software_I2C_user_guide/> for notes on multiple i2c)

### IMU

* This is where all the sensor data will come from.

* IMU:

  <https://www.seeedstudio.com/Grove-IMU-10DOF-p-2386.html>

  <https://wiki.seeedstudio.com/Grove-IMU_10DOF/>

  <https://github.com/Seeed-Studio/Seeed_Arduino_IMU10DOF>

* Functions
  * ReadPitch
  * ReadYaw
  * ReadRoll
  * ReadTime
  * ReadTemp
  * ...(etc) (whatever the board can provide) (maybe a single function that takes an argument, whatever)

### Parafoil Control

* Control of the parafoil is critical, and will likely receive the most regulatory scrutiny.

* Motor driver board:

  <https://wiki.seeedstudio.com/Grove-I2C_Motor_Driver_V1.3/>

  <https://github.com/Seeed-Studio/Grove_I2C_Motor_Driver_v1_3/>

* Functions
  * OrbiterOperateParafoil (left,right,up,down) [to operate the parafoil motors]
  * OrbiterCutDown
  * EnvelopePop
  * DeployParafoil
  * OrbiterSelfDestruct

### Power Management

Where the batteries, cells, load all comes together. The below board will be interfaced to (for data reading purposes) from the Seeduino via (I believe GPIO, but maybe i2c (check the docs)):

<http://www.switchdoc.com/sunairplus-solar-power-controllerdata-collector/>

* Functions
  * ReadVoltage
  * ReadCurrent
  * ReadAmps
  * ReadChargeLevel
  * ... (etc)

### Telemetry

This key data:

    * Charge levels
    * Full IMU data 
    * Full sensor data (light,temp,etc)

needs to be broadcast to:

    * Ground stations (via lora)
    * other aircraft (via ads-b)

* Functions:
  * (future, we have no ads-b currently) ADS-B(send) [for sending data over ads-b]
  * (future, we have no ads-b currently) ADS-B(receive) [for receiving (listening) data over ads-b] (all smarts for processing would be on pi)
  * lora(send) [to dump out telemetry to ground stations]
  * lora(receive) [to receive operator instructions]
  * IMU Sensor Read (logs to memory buffer in pi, so tile38 can read)
  * IMU Sensor Send (broadcast on air)
  * Data Logging
  * GPS Read
  * GPS Send
  * Charge Status
  * Voltage of Battery

 Technical Documentation for the above, along with example code : <https://wiki.seeedstudio.com/Seeeduino_LoRAWAN/> (unfortunately no ability to deep link on that page, so read it over for example code of Lora send/receive, battery checking etc)

### Overhead / Housekeeping

Various things will be needed for regular management/housekeeping duties.

* Functions:

  * TickleTheTail [keep the watchdog from activating]

<https://shop.switchdoc.com/collections/break-out-boards/products/switchdoc-labs-dual-watchdog-timer-board-for-arduino-raspberry-pi>>
is the hardware watchdog timer that we have in the shop.

This will be used to supervise/recover both the arduino and the pi.

## Avionics - Raspberry pi

The pi is essentially a large disk (for geo spatial db, holding avionics firmware, holding log data etc) and beefy CPU (for geo spatial lookups, processing ads-b data, perhaps some data crunching for local optimizations based on weather) attached to the avionics. The pi is important, but not completely critical. It can crash (and if not fixed by watchdog) can remain offline and the orbiter can come in for service safely. We will utilize best practices with the pi, to ensure it's as reliable/available as possible.

In poc the pi will be attached via USB (and also serving as a host for the arduino for firmware updates of the avionics ), in prototype/prod it will be on an integrated board as a Pi CM.

### Payload Power Management

Control of the power payload, on a geo fence basis, in a black box/non customer changeable is also super critical, and will likely receive the second most regulatory scrutiny.

In the prototype, the geo fence will utilize this board to cut/restore power:

<https://shop.switchdoc.com/collections/break-out-boards/products/usb-powercontrol-board-v2-w-grove-control-usb-to-usb-solid-state-relay-for-raspberry-pi-and-arduinos-v2>

* Functions
  * Payload Power On
  * Payload Power Off
  * Payload Location Lookup

### geo gencing

 <https://tile38.com/>

This will be utilized for geo-fencing. The pi will periodic query
GPS (or just read off the serial feed from the arduino) and cut off power to the payload (and restore it) at appropriate locations.

### operating system

Most likely will utilize a combination of

    * balena os https://www.balena.io/os/
    * openmtc https://www.openmtc.org/

### avionics updating

Updating the seeduino firmware image in flight, Will utilize the

* arduino-cli

system.

## Ground Station Avionics

This will be a relatively simple system, designed todo two functions:

* Receive lora broadcasts
* Convert the lora data to IP packets and push the packets to a bus for listeners to consume.

The ground station avionics software will do nothing else.

For receiving broadcasts:

* <https://github.com/RAKWireless>
* <https://github.com/RAKWireless/RAK831-LoRaGateway-RPi>

* For converting lora packets to IP and relaying data to an endpoint: <https://www.chirpstack.io/gateway-bridge/gateway/raspberrypi/>

* General lorawan software
<https://github.com/Lora-net>

* relaying to timeseries database

<https://github.com/mhe/mqtt2influxdb>

## Operations Center / Device fleet management

 We must implement one bus listener: the Ops Center. Other parties may implement additional listeners as needed.

 Primary software packages involved:

* <https://nasa.github.io/openmct/>
* <https://www.chirpstack.io/>

* Balena
  * <https://www.balena.io/os/?>
  * <https://www.balena.io/engine/?>
  * <https://www.balena.io/open/?>
  * <https://github.com/balena-io/open-balena?d_id=7632b330-6018-41b8-82ca-e5fab4fabf15R>

### Backend

### Client side
* <http://www.berryterminal.com/doku.php/berryboot>
* <http://www.berryterminal.com/doku.php/berryboot/adding_custom_distributions>

### Telemetry

* <https://richardstechnotes.wordpress.com/2015/12/26/iot-streaming-with-mqtt-and-apache-nifi/>
* <https://github.com/richards-tech/RTMQTT>
* <https://github.com/richards-tech/RTNiFiStreamProcessors>
* <https://github.com/richards-tech/RTIMULib2>
* <http://cpham.perso.univ-pau.fr/LORA/RPIgateway.html>
* <http://www.dragino.com/products/module/item/106-lora-gps-hat.html>
* <http://wiki.dragino.com/index.php?title=Lora/GPS_HAT#Example1_--_Use_with_LMIC_library_for_LoraWAN_compatible>
* <http://www.eleduino.com/Dragino-Lora-GPS_HAT-for-Raspberry-Pi-p10580.html>
* <http://www.aliexpress.com/store/product/Long-distance-wireless-433-868-915Mhz-Lora-and-GPS-Expansion-Board-for-Raspberry-Pi/1390863_32672385182.html>
* <http://csis.org/files/attachments/151216_Unmanned_Systems.pdf>

## Ground Station Payload

This will be written up later, and is for a dedicated payload team to worry about.

For knowledge capture purposes, some things to consider:

* <https://github.com/jhshi/openofdm>
* <https://openofdm.readthedocs.io/en/latest/overview.html>
* <https://news.ycombinator.com/item?id=25814237>
* <https://www.nuand.com/bladeRF-wiphy/>
* <https://news.ycombinator.com/item?id=24273919>
* <https://github.com/open-sdr/openwifi>
