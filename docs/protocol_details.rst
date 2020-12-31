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


WiFi password encryption
------------------------

1. Generate encryption key

   1. Use secret key: **supersecretkey!!**
   2. Get byte representation of MAC address of a server and repeat it to length of the secret key
   3. xor these two values

2. Encrypt

   1. Use password to access WiFi and pad it with zero bytes to length 64 bytes.
   2. Use rc4 to encrypt padded password with the *encryption key*

3. Encode

   Base64 encode encrypted string.


WiFi SSID encryption
--------------------

Doesn't seem to use same algorithm as for WiFi password encryption. Maybe only different key?


Where lookup
------------

It is unclear what this is used for. Maybe get MAC address?

Application sends UDP broadcast to ports 5555 and 5556 with message **\\x01WHEREAREYOU** (first character is byte with hex representation 0x01).

Discovery
---------

This seems to be used to find all Twinkly devices on the network.

1. Application sends UDP broadcast to port 5555 with message **\\x01discover** (first character is byte with hex representation 0x01).
2. Server responds back with following message:

   - first four bytes are octets of IP address written in reverse - first byte is last octet of the IP adress, second second to last, ...

   - fifth and sixth byte forms string "OK"

   - rest is string representing `device id`_ padded with zero byte.


Get and verify authentication token
-----------------------------------

Application uses TCP port 80 to get and verify authentication token. It is later used for some calls that require it.

1. Application generates challenge and sends it as part of login request.
2. Among other data server responds with authentication token
3. Application uses authentication_token in header of request to verify.

Only after this handshake authentication token can be used in other calls. Most of them require it. Previous authentication token, if existed, is invalided.

Verification of challenge-response
----------------------------------

As part of login process server sends not only authentication token but also challenge-response. Application may verify if it shares secret with server - maybe if it is genuine Twinkly device with following algorithm:

1. Generate encryption key

   1. Use secret key: **evenmoresecret!!**
   2. get byte representation of MAC address of a server and repeat it to length of the secret key
   3. xor these two values

2. Encrypt - use rc4 to encrypt challenge with the key

3. Generate hash digest - encrypted data with SHA1

4. Compare - hash digest must be same as challenge-response from server


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


LED effect operating modes
--------------------------

Hardware can operate in one of following modes:

- off - turns off lights
- demo - starts predefined sequence of effects that are changed after few seconds
- movie - plays last uploaded effect
- rt - receive effect in real time

First two are set just by API call.


Upload full movie LED effect
----------------------------

1. Application calls API to switch mode to movie
2. Application calls API movie/full with file sent as part of the request
3. Application calls config movie call with additional parameters of the movie


Movie file format
-----------------

LED effect is called **movie**. It consists of **frames**. Each frame defines colour of each LED.

Movie file format is simple sequence of bytes. Three bytes in a row represent intensity of *red*, *green* and *blue* in this order. Each frame is defined just with number of LEDs times three. Frames don't have any separator. Definition of each frame starts from LED closer to LED driver/adapter.


Real time LED operating mode
----------------------------

1. Application calls HTTP API to switch mode to rt
2. Then UDP packets are sent to a port 7777 of device. *Each packet represents single frame* that is immediately displayed. See bellow for format of the packets.
3. After some time without any UDP packets device switches back to movie mode.


Real time LED UDP packet format
-------------------------------

Before packets are sent to a device application needs to login and verify authentication token. See above.

Each UDP has header:

* 1 byte *\\x01* (byte with hex representation 0x01)
* 8 bytes Base 64 decoded authentication token
* 1 byte number of LED definitions in the frame

Then follows body of the frame similarly to movie file format - three bytes for each LED.

For my 105 LED each packet is 325 bytes long.


Scan for WiFi networks
----------------------

Hardware can be used to scan for available WiFi networks and return some information about them. I haven't seen this call done by the application so I guess it can be used to find available channels or so.

1. Call network scan API
2. Wait a little bit
3. Call network results API
