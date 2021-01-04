Bluetooth API reference
=======================

Overview
--------

Generation II devices have Bluetooth LE module that can be used to configure
WiFi network. User documentation how to `configure Twinkly generation II for
the first time`_ can be found on the manufacturer's site.

Protocol is based on `Espressif BluFi`_ solution with couple of differences
described here.

Device doesn't require data encryption nor it use data checksum.

Device broadcasts its Device ID (see Protocol Details). Bluetooth adapter has
MAC address that differs in right most byte from WiFi MAC address.

GATT information
----------------

The most interesting handles are:

- 0x2a for writing (client -> device)
- 0x2c for reading and notification (device -> client):

+--------------+---------------------------------------+--------------+----------------+
|   Handles    |      Service > Characteristics        |  Properties  |      Data      |
+==============+====+==================================+==============+================+
| 0001 -> 0005 | Generic Attribute (1801)              |              |                |
+--------------+----+----------------------------------+--------------+----------------+
| 0003         |    | Service Changed (2a05)           | INDICATE     |                |
+--------------+----+----------------------------------+--------------+----------------+
| 0014 -> 001c | Generic Access (1800)                 |              |                |
+--------------+----+----------------------------------+--------------+----------------+
| 0016         |    | Device Name (2a00)               | READ         | Twinkly_1CC190 |
+--------------+----+----------------------------------+--------------+----------------+
| 0018         |    | Appearance (2a01)                | READ         | Unknown        |
+--------------+----+----------------------------------+--------------+----------------+
| 001a         |    | 2aa6                             | READ         | 00             |
+--------------+----+----------------------------------+--------------+----------------+
| 0028 -> ffff | 68cac0661f6e0f961146da9d00fa7b1b      |              |                |
+--------------+----+----------------------------------+--------------+----------------+
| 002a         |    | 68cac0661f6e0f961146da9d01fa7b1b | WRITE        |                |
+--------------+----+----------------------------------+--------------+----------------+
| 002c         |    | 68cac0661f6e0f961146da9d02fa7b1b | READ, NOTIFY | 00             |
+--------------+----+----------------------------------+--------------+----------------+

Control frames
--------------

Connect the device to the AP
````````````````````````````

LSB - Type: `0x0c`

WiFi mode and its status
````````````````````````

LSB - Type: `0x14`

Example

.. code-block:: text

    14 00 00 00

Set the opmode of WiFi
```````````````````````

LSB - Type: `0x1d`

Example in AP workflow

.. code-block:: text

    1d 00 00 02 04 00

Example in STA workflow

.. code-block:: text

    1d 00 00 01 00

Disconnect the BLE GATT link
````````````````````````````

LSB - Type: `0x20`

WiFi List
``````````

LSB - Type: `0x24`

Example

.. code-block:: text

    24 00 00 00

Data frames
-----------

Unknown use
```````````

LSB - Type: `0x08`

Example

.. code-block:: text

    08 08 00 02 01 00

Send the SSID for STA mode
``````````````````````````

LSB - Type: `0x09`

Send the password for STA mode
``````````````````````````````

LSB - Type: `0x0d`

.. _`Espressif BluFi`: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-guides/blufi.html
.. _`configure Twinkly generation II for the first time`: https://web.archive.org/web/2/https://www.twinkly.com/knowledge/configure-twinkly-generation-ii-for-the-first-time/
