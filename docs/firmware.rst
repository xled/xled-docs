.. _firmware:

Firmware
========

This page desribes firmware that this documentation has been tested with.
Firmware can be upgraded over the network.

Generation I
------------

Firmware family "D"
```````````````````

Firmware consists of two files. I have seen following versions:

+-------------+-----------------------------------------------------------------------------------------+
| Version     | Notes                                                                                   |
+=============+=========================================================================================+
| 1.99.18     | Probably first public version                                                           |
+-------------+-----------------------------------------------------------------------------------------+
| 1.99.20     |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 1.99.24     |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 1.99.30     |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.0       |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.8       |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.12      |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.13      |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.19      |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.0.22-mqtt | Adds MQTT support                                                                       |
+-------------+-----------------------------------------------------------------------------------------+
| 2.1.0       |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.1.1-net   |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.1.2-net   |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.3.5       | Last version offered by application for upgrade                                         |
+-------------+-----------------------------------------------------------------------------------------+
| 2.3.8       |                                                                                         |
+-------------+-----------------------------------------------------------------------------------------+

Generation II
-------------

Firmware file is `encrypted using host generated key`_. 

I have seen following versions:

+---------+------------+------------+---------------------------------------------------------------------+
| Version | Available for Family    | Notes                                                               |
|         +------------+------------+                                                                     |
|         | "F"        | "G"        |                                                                     |
+=========+============+============+=====================================================================+
| 2.2.1   | Yes        |            | Shipped with family "F" device in 2020, maybe first public version? |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.2.2   | Yes        |            |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.2   | Yes        |            | Adds MQTT and brightness support                                    |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.6   | Yes        |            | Seems to incorrectly report `frame_rate: 1` in `gestalt` API call   |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.14  | Yes        |            |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.16  | Yes        |            |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.21  | Yes        | Yes        | Shipped with family "G" device                                      |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.22  | Yes        |            |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.25  | Yes        | Yes        |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.4.30  | Yes        | Yes        | Introduced SSID encryption for client setup                         |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.5.6   | Yes        | Yes        | Adds UUIDs to effects                                               |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.5.9   | Yes        | Yes        |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.6.0   | Yes        | Yes        |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.6.2   | Yes        | Yes        | Shipped with family "F" device in 2021                              |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.7.1   | Yes        | Yes        |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+
| 2.8.3   | Yes        | Yes        |                                                                     |
+---------+------------+------------+---------------------------------------------------------------------+

.. _`encrypted using host generated key`: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/security/flash-encryption.html#using-host-generated-key
