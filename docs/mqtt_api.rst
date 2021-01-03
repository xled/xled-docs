Messaging API reference
=======================

New in firmware version 2.0.22.

Overview
--------

Device sends messages with its states to a broker with MQTT. By default broker is set to mqtt.twinkly.com.

Firmware family "D" use HTTP and "G" use TLS.

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

Online (firmware 2.0.22)::

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

Parameters
----------

Parameters published by a device to broker.

Topic
`````

`xled/params/` followed by client ID

Messages
````````

`brightness`
	(number), brightness value in percent
`filters`
	(list), contains a string "brightness" if brightness is set.

Example
```````

::

	{"brightness": 50, "filters": ["brightness"]}

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

`setbrightnessdelta`
	changes brightness by amount set by `value`. Parameter `value` is signed number.

`setbrightness`
	changes brightness to `value`. Parameter `value` is a number between 0..255 but only up to 100 has a dimming effect.

Example
```````

Switch on::

	{"command": "switchon"}

Lower brightness value by 20 percents::

	{"command": "setbrightnessdelta", "value", -20}
