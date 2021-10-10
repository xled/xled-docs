Protocol details
================

This page describes hardware, modes of operation and some private protocols or algorithms used by Twinkly application.

Hardware ID
-----------

Each device has unique hardware ID that consists of 6 bytes written as lowercase hex value. It can be read through API call.

* In generation I devices it is rightmost part of device's MAC address.

* In generation II devices it is rightmost part of the device's MAC address in station (STA) mode.

Device ID
---------

Each device has unique ID that consists of a prefix **Twinkly_** and 6 bytes written as uppercase hex value:

* In generation I devices it is rightmost part of device's MAC address.

* In generation II devices it is rightmost part of the device's MAC address in access point (AP) mode.

Value can not be directly read by API but it is used in various places - e.g. in discovery packets.

Device name
-----------

Device has a name that can be used for identification - e.g. in the application. Its default value is the same as `device id`_. It can be read or changed by API. It's length is from 0 to 32 bytes.

Access point SSID
-----------------

Device in Access Point mode broadcasts SSID. Its default value is the same as `device id`_. It can be read or changed with API. It's length is from 1 to 31 bytes.

MQTT Client ID
--------------

When device uses MQTT protocol it sends client identification. Its default value is written as uppercase 12 byte long hex value:

* In generation I devices from their MAC address.

* In generation II devices from their MAC address in access point (AP) mode.

It can be read or changed by API. It's length is from 0 to 32 bytes.

Modes of network operation
--------------------------

Hardware works in two network modes:

- Access Point (AP)
- Station (STA)

AP mode is default - after factory reset. See `Access point SSID` for details. Generation I devices provide open AP - without any encryption. Generation II devices use WPA2 with password `Twinkly2019`. Server uses static IP address 192.168.4.1 and operates in network 192.168.4.0/24. Provides DHCP server for any device it joins the network. It's hostname is `_gateway`.

In STA mode a device connects to an access points with configured SSID and password. If DHCP is enabled it sends `device id`_ hostname.

Network mode can be changed with an API calls.

http://41j.com/blog/2015/01/esp8266-access-mode-notes/

WiFi setup encryption
---------------------

Some WiFi configuration is encrypted before it is sent over REST API to the device.

Up until firmware version 2.4.22 only WiFi password was encrypted using key string `supersecretkey!!`, which is 16 bytes long, in hex::

    73 75 70 65 72 73
    65 63 72 65 74 6B
    65 79 21 21

Since firmware version 2.4.25 not only WiFi password but also SSID is encrypted with new key of length 48 bytes, in hex::

    26 80 F5 87 9F EE
    2C 75 11 AA 08 15
    47 44 8E 04 99 CD
    68 07 6E 09 32 62
    5D C4 DE 7C 38 98
    9E 88 80 EE 2A B7
    33 67 8F A2 0D CC
    85 D8 94 CD 94 4F

1. Generate encryption key

   1. Get MAC address of a device in AP mode or `mac` value from `gestalt` REST API call and repeat it to length of the secret key.
   2. XOR these two values.

2. Right pad desired configuration (password or SSID) with zero bytes to length 64 bytes.

3. Use RC4 to encrypt padded data with the encryption key.

4. Base64 encode encrypted string.

Discovery protocol
------------------

Discovery protocol uses IPv4 packets to broadcast addresses over UDP from port 5555 and listens on port 5555 for replies.

Discovery request message to find all Twinkly devices on the network:

* 1 byte `0x01` probably version
* 8 bytes as a string `discover`

Twinkly devices respond with message:

* first four bytes are octets of IP address of the device in reverse order - first byte is last octet of the IP adress, second one is the second to last, ...

* fifth and sixth byte are `0x79 0x75` - string `OK`

* rest is a string representing `device id`_

* last one is a zero byte

Where are you protocol
----------------------

Where are you protocol uses unicast IPv4 packets over UDP to port 5556.

Request message so far with unknown purpose:

* 1 byte `0x01` probably version
* 8 bytes as a string `WHEREAREYOU`

Get and verify authentication token
-----------------------------------

Application uses TCP port 80 to get and verify authentication token. It is later used for some calls that require it.

1. Application generates challenge and sends it as part of login request.
2. Among other data server responds with authentication token
3. Application uses authentication_token in header of request to verify.

Only after this handshake authentication token can be used in other calls. Most of them require it. Previous authentication token, if existed, is invalided.

Verification of challenge-response
----------------------------------

As part of login process server sends not only authentication token but also challenge-response. Application may verify if it shares secret with server - maybe if it is genuine Twinkly device.

The key for this algorithm is a string `evenmoresecret!!` which is 16 long, in hex::

    65 76 65 6E 6D 6F
    72 65 73 65 63 72
    65 74 21 21

1. Generate encryption key

   1. Get MAC address of a device in AP mode or `mac` value from `gestalt` REST API call and repeat it to length of the secret key.
   2. XOR these two values

2. Encrypt - use RC4 to encrypt challenge with the key.

3. Generate hash digest - encrypted data with SHA1.

4. Compare - hash digest must be same as challenge-response from server.

Firmware update
---------------

Update sequence for generation I device follows:

1. application sends first file to endpoint 0 over HTTP
2. server returns sha1sum of received file
3. application sends second file to endpoint 1 over HTTP
4. server returns sha1sum of received file
5. application calls update API with sha1sum of each stages.

Update sequence for generation II device follows:

1. application sends first file to endpoint 0 over HTTP
2. server returns sha1sum of received file
3. application calls update API with sha1sum of uploaded stage.

LED operating modes
-------------------

Hardware can operate in one of following modes:

* `off` - turns off lights
* `demo` - starts predefined sequence of effects that are changed after few seconds
* `movie` - plays predefined or uploaded effect. If movie hasn't been set (yet) code 1104 is returned.
* `rt` - receive effect in real time
* `effect` - plays effect with `effect_id`
* `playlist` - plays a movie from a playlist. Since firmware version 2.5.6.

Upload full movie LED effect
----------------------------

1. Application calls API to switch mode to movie
2. Application calls API movie/full with file sent as part of the request
3. Application calls config movie call with additional parameters of the movie

Frame format
------------

A frame is the lowest level that a device accepts to light any LED. Firstly let's start with single LED definition.

Single LED
``````````

Intensity of each color in a LED is defined by one unsigned byte. Order of bytes is based on a LED profile:

- RGB:

  1. *red*
  2. *green*
  3. *blue*

- RGBW:

  1. *white*
  2. *red*
  3. *green*
  4. *blue*

Frame
`````

The frame is a sequence of bytes that define color of each LED in a device. First LED is the closest to a LED driver/adapter (potentially on one of the cables if a device has two).

Examples of frame lengths:

+----------------+-------------+-----------------------+
| Number of LEDs | LED profile | Frame length in bytes |
+================+=============+=======================+
|            105 |         RGB |                   315 |
+----------------+-------------+-----------------------+
|            210 |        RGBW |                   840 |
+----------------+-------------+-----------------------+

Movie format
------------

A movie is a sequence of frames. A frame rate is defined separately from a movie.

Example movie lengths:

+-----------------------+------------------+-----------------------+
| Frame length in bytes | Number of frames | Movie length in bytes |
+=======================+==================+=======================+
|                   315 |               12 |                  3780 |
+-----------------------+------------------+-----------------------+
|                   840 |                6 |                  5040 |
+-----------------------+------------------+-----------------------+

Real time LED operating mode
----------------------------

1. Application calls HTTP API to switch mode to rt
2. Then UDP packets are sent to a port 7777 of device. Each packet contains a frame or its segment that is immediately displayed. See bellow for format of the packets.
3. After some time without any UDP packets device switches back to movie mode.

Real time LED UDP packet format
-------------------------------

Before packets are sent to a device application needs to login and verify authentication token. See above.

UDP packet format depends on firmware version which implies device generation.

Version 1
`````````

This format is used in generation I devices. An UDP packet starts with a header:

* 1 byte: version *\\x01* (byte with hex representation 0x01)
* 8 bytes: byte representation of the authentication token - not encoded in base 64
* 1 byte: number of LED definitions in the frame

Then follows a body in the frame format.

Version 2
`````````

This format is used in generation II devices until firmware version 2.4.6 (including). An UDP packet starts with a header:

* 1 byte: version *\\x02* (byte with hex representation 0x02)
* 8 bytes: byte representation of the authentication token - not encoded in base 64
* 1 byte: *\\x00* of unknown meaning

Then follows a body in the movie format.

Version 3
`````````

This format is used in generation II devices from firmware version 2.4.14.

Frames are split into fragments with size up to 900 bytes. Each fragment is sent in an UDP packet that starts with a header:

* 1 byte: version *\\x03* (byte with hex representation 0x03)
* 8 bytes: byte representation of the authentication token - not encoded in base 64
* 2 bytes: *\\x00\\x00* of unknown meaning - maybe a fragment could be 3 bytes long?
* 1 byte: frame fragment number - first one is 0

Then follows a body with the frame fragment.

Example of a frame with size 2250 bytes for a device with 750 LEDs with RGB LED profile:

+-----------------------+---------------------+
| Frame fragment number | Frame fragment size |
+=======================+=====================+
|                     0 |                 900 |
+-----------------------+---------------------+
|                     1 |                 900 |
+-----------------------+---------------------+
|                     2 |                 450 |
+-----------------------+---------------------+

Scan for WiFi networks
----------------------

Hardware can be used to scan for available WiFi networks and return some information about them. I haven't seen this call done by the application so I guess it can be used to find available channels or so.

1. Call network scan API
2. Wait a little bit
3. Call network results API

Group devices
-------------

Devices can be grouped together to control them at once. Only compatible devices can be grouped. Compatibility seems to be based on LED profile (RGB vs. RGB+W).

One device is master and other are slaves.

Group name acts as a single device in the application.

Master since firmware version 2.5.6 grouped with one slave in compat mode sends every 5 seconds broadcast packets from UDP port 7777 to UDP port 7777 with total length 50 bytes. Every time 3 packets of the same contents are sent.

Header:

* 1 byte *\\x03* (byte with hex representation 0x03)
* 8 bytes *\\x0000000000000000*

Followed by one of:

a) First triplet:

* 2 bytes *\\x0101*
* 4 bytes that seem to be based on group name
* 3 bytes of unknown meaning
* 4 bytes that seem to be constant for a device also used in the next packet

b) Second triplet:

* 2 bytes *\\x0104*
* 4 bytes that seem to be based on group name
* 2 bytes of unknown meaning
* 4 bytes that seem to be constant for a device also same as in previous packet

c) Third or later:

* 2 bytes *\\x0102*
* 4 bytes that seem to be based on group name
* 2 bytes that change every 10 seconds

Rest is padded with *\\x00*
