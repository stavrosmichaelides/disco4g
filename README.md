# About disco4g

Disco4g is a LTE/4G software mod for Parrot Disco drone. It adds capability to connect Disco with SkyController 2 (SC2) and Free Flight Pro app (FFP) over 4G datalink - and possibly operate drone beyond its wifi coverage limits. Both control/telemetry and live video should be working - given that enough 4G data bandwidth is available in flying area.

What needs to be modded:
* Disco firmware
* SkyController 2 firmware (optional)

P.S. This mod might be potentially useful also for Parrot Bebop 2 owners - as these drones share quite a lot. But just a wild guess atm ... :)

## Parrot Disco over LTE/4G Demo (Youtube)

[![Parrot Disco over LTE/4G Demo](https://img.youtube.com/vi/1Txyy7Xstms/0.jpg)](https://www.youtube.com/watch?v=1Txyy7Xstms)

## Status

NB! This is still an EXPERIMENTAL hack, no thorough testing has been conducted! Mod and fly your Disco entirely at YOUR OWN RISK!!!

What seems to be working so far:
* Drone discovery and connection initialization - which for some reason takes about 3x longer than normally (10s vs 30s)
* Receiving video stream in FFP app (expect higher latency due 4G nature)
* Recovering datalink when 4G disconnects/reconnects happen
* Switching from Wifi to 4G profile and vice versa (manually from FFP app)

TODOs:
* Clean up scripts and add comments
* Introduce main config files and reconfiguration at boot
* Write installers
* Improve documentation
* Test and optimize

## Requirements

* Parrot Disco drone (firmware v1.4.1)
* Parrot SkyController 2 (firmware v1.0.7)
* Raspberry Pi 2/3 (OS Rasbian Stretch Lite)
* USB Wifi dongle for Raspberry Pi with AP and IE (Information Elements) capabilities
* 2x Huawei E3372h LTE/4G USB dongles
* 2x 4G SIMs with data plans capable of video streaming
* Cloud VM with public IP for VPN server - or attach public IP to one of the 4G SIMs (depends also on your mobile operator)

## Theory of Operation

![lte mod diagram](images/lte-mod-diagram.png)

* RPi acts as a Wifi Access Point simulating Parrot Disco drone for SkyController 2 (and for Free Flight Pro app)
* RPi then forwards all SC2/FFP traffic to/from real Disco over 4G datalink - once SC2 connects to RPi AP
* In order for 4G datalink to work over NATed connections (normally the case with 4G connections) Peer-to-Peer VPN layer is used - that handles NAT traversal and falls back to stream proxying via mediator server - if other methods for achieving direct streams will not work
* P2P VPN Server with public IP has two objectives: to exchange clients connection information and to try achieve direct p2p connections between them -- and support failback to simple proxying
* Disco drone has USB 4G dongle onboard, connected to CHUCK - and can talk to SC2/FFP over 4G when drone profile is switched to RPi "fake drone" (from FFP app)

## Installation instructions

* [Disco/CHUCK](Disco/README.md)
* [SkyController 2](SC2/README.md)
* [Raspberry Pi](RPi/README.md)
* [VPN Server](VPN/README.md)

## Usage instructions

Pre-flight checks and bootup procedure:
* Verify that you have sufficient 4G signal strength, acceptable ping latency and data bandwith available in the flying area
* Verify that sufficient dataplans are available for your 4G SIM cards. 1080p video stream is roughly about 2.4 Mbits per second => 0.3 MB/sec => 18 MB/min => 1.080 GB/h 
* Verify that RPi has sufficient battery power (ie like 10.000 mAH / 2A 5V powerbank fully charged)
* Boot up Disco and verify that 4G USB dongle led indicates successful connection to carrier (in case of Huawei e3372h: solid blue or green, not blinking)
* Boot up RPi with 4G dongle connected and verify that dongle led indicates successful connection to carrier and that PISCO AP is listed when doing WIFI scan
* Boot up SC2 and connect it to FFP app: discover and connect to PISCO drone
* Verify that SC2 led turns blue (if SC2 was modded) and that FFP connects to PISCO and video stream starts and is stable (can take up to 30-40 secs)
