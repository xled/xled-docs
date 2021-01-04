.. _hardware:

Hardware
========

There are wide variety of Twinkly products on the market with various
functionality and properties. This page desribes hardware that this
documentation has been tested with.

Generation I
------------

Hardware consists of two circuit boards:

- Module ESP-01 with microcontroller ESP8266 by Espressif Systems
- Custom-made LED driver module

Esptool v2.8 identification of MCU:

- Chip is ESP8266EX
- Features: WiFi
- Crystal is 26MHz

and flash:

- Manufacturer: 5e
- Device: 4015
- Detected flash size: 2MB

Model TW105S-EU
```````````````

It is a string of 105 RGB LED from 2017.

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

Generation II
-------------

Hardware consists of a circuit board with ESP32 microcontroller by Espressif
Systems. Esptool v2.8 identification of MCU:

- Chip is ESP32D0WDQ5 (revision 1)
- Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
- Crystal is 40MHz

and flash:

- Manufacturer: c8
- Device: 4017
- Detected flash size: 8MB

On the top of that device has Bluetooth for out of the band configuration of
WiFi.

Model TWW210SPP-TEU
```````````````````

It is branded as `Twinkly Wall and consists of 210 RGB+W LEDs`_. It is a matrix
of 10 strings each consisting of 21 red, green, blue and white LEDs.

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

In this documentation the device is referred as:

- Hardware ID: 1bb210
- MAC address 98:f4:ab:1b:b2:10

Model TWI190SPP-TEU
```````````````````

It is branded as `Twinkly Icicle and consists of 190 RGB+W LEDs`_. It is a
repeated pattern of strings with length 2, 4, 6, 2, 5 where each has red,
green, blue and white LEDs.

API exposes these details:

- Product code: TWI190SPP
- Hardware version: 100
- Flash size: 64
- LED Type: 12
- Firmware family: G
- Bytes per LED: 4
- Max Supported LEDs: 1200
- Frame Rate: 28.57
- Movie Capacity: 992
- Wire type: 4

In this documentation the device is referred as:

- Hardware ID: 1cc190
- MAC address 98:f4:ab:1c:c1:90

Model TWF020STP-BEU
```````````````````

It is branded as `Twinkly Festoon and consists of 20 RGB Lamps`_. It is a
string where each lamp is red, green and blue LEDs.

API exposes these details:

- Product code: TWF020STP-BT
- Hardware version: 100
- Flash size: 64
- LED Type: 14
- Firmware family: F
- Bytes per LED: 3
- Max Supported LEDs: 510
- Frame Rate: 200
- Movie Capacity: 1984

In this documentation the device is referred as:

- Hardware ID: 1fe520
- MAC address 98:f4:ab:1f:e5:20

Model TWS250STP-BEU
```````````````````

It is branded as `Twinkly Strings 250 LEDs Multicolor`_ with 250 red, green and
blue LEDs.

API exposes these details:

- Product code: TWS250STP
- Hardware version: 100
- Flash size: 64
- LED Type: 14
- Firmware family: F
- Bytes per LED: 3
- Max Supported LEDs: 510
- Frame Rate: 30.3
- Movie Capacity: 1984

In this documentation the device is referred as:

- Hardware ID: 2bb250
- MAC address 98:f4:ab:2b:b2:50

.. _`Twinkly Wall and consists of 210 RGB+W LEDs`: https://web.archive.org/web/2/https://www.twinkly.com/products/curtain-special-edition-210-leds/
.. _`Twinkly Icicle and consists of 190 RGB+W LEDs`: https://web.archive.org/web/2/https://www.twinkly.com/products/icicle-190-leds-special-edition/
.. _`Twinkly Festoon and consists of 20 RGB Lamps`: https://web.archive.org/web/2/https://www.twinkly.com/products/festoon-lights-starter-pack/
.. _`Twinkly Strings 250 LEDs Multicolor`: https://web.archive.org/web/2/https://www.twinkly.com/products/strings-multicolor-250-leds/
