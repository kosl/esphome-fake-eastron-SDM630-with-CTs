
## ESPHome Fake Eastron SDM630 Modbus v2 with CTs
[![GitHub Activity][commits-shield]][commits]
[![Last Commit][last-commit-shield]][commits]
[![Platform][platform-shield]](https://github.com/esphome)

This project [kosl/esphome-fake-eastron-SDM630-with-CTs](https://github.com/kosl/esphome-fake-eastron-SDM630-with-CTs) allows you to provide live data from current transformers (CTs) to a solar inverter that expects an [Eastron SDM630](https://www.eastroneurope.com/products/view/sdm630modbus).

Such example is Deye inverter

![Grid-tie inverter](images/deye-with-grid-tie-inverter.png)

We will use ESP32-C3 SuperMini board with addition of "external" CTs and few resistors as shown below 

![CTsr](images/current-transformers.png)

Since this will be installed in SUN-12K-SG04LP3 inverter we will also 
add local [Modbus dongle](https://github.com/davidrapan/esphome-modbus_bridge) 
for use in [Solarman](https://github.com/davidrapan/ha-solarman) integration.

## How is this working?

The project provides an ESPhome component acting as a server that can be polled by a master (e.g. a solar inverter) via Modbus RTU. It behaves as much as possible like the Eastron SDM630 while fetching the actual data from the CTs.

### How exactly?

* On boot, the ESPhome starts a Modbus slave on address 2. This is where Deye expects to find the vanilla Eastron SDM630 Modbus V2. It registers several input registers that can be queried by a Modbus master.
* At 1s intervals, it makes a request to CTs fetching live data.
* CT values are parsed and converted to IEEE-754 float and written to the matching registers.
* Voltages for each phase can be calibrated from Deye inverter through Modbus or by Home Assistant integration.
* The inverter queries the Modbus slave several times a second, fetching the input registers in various groups.

## Why?

I use a SUN-12K-SG04LP3-EU hybrid inverter. The inverter only supports two types of smart meters, which must be connected via RS485:

* Eastron SDM630 Modbus V2 or V3
* CHINT smart meter from Growatt

In my case the inverter is installed less than 15m from the grid connection. 

Since the ESP32-C3 is very cheap this simple ESPhome project bridges manufacturer, distance, physical layer and protocol.

## Implementation
For CP signaling the cheapest ESP32-C3 Super Mini module (€2.5-€4), some resistors, and RS485 to TTL module(s) are required.
Compete cost is under €10 depending on the cost of CT type that can be various (cheap, split-core, different ratios, accuracy,...)

## Installation

1. Connect ESP32 dev board to RS485 module.
2. Connect RS485 A and B connectors to pins 5 (A) and 4 (B) of the Deye RJ45 Port for RS 485.
3. Build and flash the firmware based on the [sample ESPhome config](./fake-eastron.yaml).
4. Enable meter-reading in Deye  **TODO: explain how**

### Modbus address

Deye expects different smart meters at different slave addresses:

| Meter | Phases | Slave address |
|---------|----------|-----------------|
| Eastron SDM630 v2 | 3 | 2 |
| Eastron SDM630 v3 | 3 | 3 |

Eastron SDM630 **v3** is a custom version with firmware influenced by Growatt. My understanding is that it allows a higher rate of request/responses, resulting in finer tracking of power demands.

## External documentation & tools

* [Eastron SDM630 Modbus Protocol](docs/SDM630-Modbus_Protocol.pdf)
* [ESP32 NodeMCU pinout](docs/ESP-32_NodeMCU_Developmentboard_Pinout.pdf)
* [IEEE-754 Floating Point Converter](https://www.h-schmidt.net/FloatConverter/IEEE754.html)
* [Online Modbus Parse](https://rapidscada.net/modbus/)
* Python tool to query an Eastron; for testing/verification: [https://github.com/nmakel/sdm_modbus](https://github.com/nmakel/sdm_modbus)


[releases-shield]: https://img.shields.io/github/v/release/kosl/esphome-fake-eastron-SDM630-with-CTs
[commits-shield]: https://img.shields.io/github/commit-activity/m/kosl/esphome-fake-eastron-SDM630-with-CTs
[last-commit-shield]: https://img.shields.io/github/last-commit/kosl/esphome-fake-eastron-SDM630-with-CTs
[platform-shield]: https://img.shields.io/badge/platform-ESPHome-blue

[releases]: https://github.com/kosl/esphome-fake-eastron-SDM630-with-CTs/releases
[commits]: https://github.com/kosl/esphome-fake-eastron-SDM630-with-CTs/commits/main
