.. _hardware:

Hardware
========

There are wide variety of Twinkly products on the market with various
functionality and properties. This page desribes hardware that this
documentation has been tested with.

Generation I model TW105S-EU
----------------------------

It is a string of 105 RGB LED from 2017. Hardware consists of two circuit
boards:

- Module ESP-01 with microcontroller ESP8266 by Espressif Systems.
- Custom-made LED driver module

API exposes these details:

- Product code: TW105SEUP06
- Product version: 2
- Hardware version: 6
- Flash size: 16
- LED Type: 6
- LED Version: 1
- Max Supported LEDs: 255
- Movie Capacity: 719
- Frame Rate: 25

Firmware belongs to family "D". In this documentation the device is referred
as:

- Hardware ID: 0033aaff
- MAC address 5c:cf:7f:33:aa:ff

Generation II model TWW210SPP-TEU
---------------------------------

It is branded as Twinkly Wall and consists of 210 RGB+W LEDs. It is a matrix of
10 strings each consisting of 21 red, green, blue and white LEDs. Most likely
the device is built on the top of ESP32 microcontroller by Espressif Systems.

API exposes these details:

- Product code: TWW210SPP
- Hardware version: 100
- Flash size: 64
- LED Type: 12
- Firmware family: G
- Bytes per LED: 4
- Max Supported LEDs: 1200
- Frame Rate: 11
- Movie Capacity: 992
- Wire type: 1

On the top of that device has Bluetooth for out of the band configuration of
WiFi. In this documentation the device is referred as:

- Hardware ID: 1bb210
- MAC address 98:f4:ab:1b:b2:10
