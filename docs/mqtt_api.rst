=======================================
Twinkly private messaging API reference
=======================================

Overview
--------

Since firmware version 2.0.22-mqtt device sends messages with its states to a
broker with MQTT. By default broker is set to mqtt.twinkly.com.

Last topic levels are always client ID which are by default derived from MAC address of the device as uppercased hexadecimal digits.

Status from device
------------------

Device publishes these messages to a broker.

Topic `xled/status/` followed by client ID

Messages
````````

  * `online`

    * `ip` - IP address. Added in firmware 2.1.0.
    * `ssid` - SSID to which device is connected. Added in firmware 2.1.0.

  * `offline`

Example
```````

Online (firmware 2.0.22-mqtt)::

    {"status": "online"}

Online (since firmware 2.1.0)::

    {"status": "online", "ip": "192.168.4.1", "ssid": "home"}

Offline::

    {"status": "offline"}


Application status from device
------------------------------
Device publishes these messages to a broker.

Topic
`````

`xled/appstatus/` followed by client ID

Messages
````````

  * `off`
  * `movie`
  * `collision`
  * `rainbow`
  * `twinkle`
  * `snake`

Example
```````

Rainbow::

    {"appstatus": "rainbow"}


Command messages to device
--------------------------

Device listens to these messages.

Topic
`````

`xled/command/` followed by client ID

Messages
````````

`changeeffect`
    each message switches to next of default effects or movie. Same as pressing the button on the device.
`setmovie`
    sets to uploaded movie effect
`setcollision`
    sets default effect "collision"
`setrainbow`
    sets default effect "rainbow"
`setsnake`
    sets default effect "snake"
`settwinkle`
    sets default effect "twinkle"
`setwaves`
    sets default effect "waves"
`switchoff`
    switches off the device
`switchon`
    switches on the device

Example
```````

Switch on::

    {"command": "switchon"}
