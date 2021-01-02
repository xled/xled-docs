RESTful API reference
=====================

Overview
--------

Twinkly rest API is primary way to get information about the device, configure network and modes of the device. It is a HTTP 1.1 based API sent over TCP port 80.

This API is used by mobile applications. It haven't been made public yet so it may change at any time.


Request Authentication
``````````````````````

Most API requests require valid authentication token. Except of:

* login
* gestalt
* fw version

If API requires authentication but valid token wasn't passed server returns HTTP status code 401 Unauthenticated and string `Invalid Token.` in the response body.


HTTP Responses
``````````````

The HTTP response can be used to determine if the request was successful, and if not, whether the request should be retried.

200 Success
	The request was successful.

401 Unauthenticated
	Request requires authentication but authorization failed. Application didn't handle the request.

404 Not Found
	If a string `Resource not found.` is found in the response body check if API endpoint is implemented in the current firmware version.


Application hierarchy
`````````````````````

Endpoints seem to be organized into hierarchy by applications. Overview of the hierarchy:

* `login`
* `verify`
* `logout`
* `gestalt`
* `device_name`
* `echo`
* `timer`
* `led`

  * `mode`
  * `effects`

    * `current`

  * `config`
  * `movie`

    * `full`
    * `config`

  * `out`

    * `brightness`

  * `driver_params`
  * `reset`
  * `reset2`

* `fw`

  * `version`
  * `update`
  * `0`

    * `update`

  * `1`

    * `update`

* `network`

  * `scan`
  * `scan_results`
  * `status`

* `mqtt`

  * `config`

Application responses
`````````````````````

The API may return application status as `code` value of JSON. Returned will not necessarily "correspond" with the HTTP status code. For example, a HTTP status code 200 OK returned with an error application code indicates that the request successfully reached the server, but application cannot process the request.

1000
	Ok

1001
	Error

1101
	Invalid argument value

1102
	Error

1103
	Error - value too long? Or missing required object key?

1104
	Error - malformed JSON on input?

1105
	Invalid argument key

1107
	Ok?

1108
	Ok?

1205
	Error with firmware upgrade - SHA1SUM does not match


Login
-----

Request access token.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/login`

Parameters
``````````

Parameters as JSON object.

`challenge`
	Random 32 byte string encoded with base64.


Response
````````

The response will be an object.

`authentication_token`
	Access token in format: 8 byte string base64 encoded. First authenticated API with this token must be Verify.

`challenge-response`
	41 byte string ([0-9a-h])

`code`
	(integer), application return code.

`authentication_token_expires_in`: integer. All the time 14400?


Example
````````

Request::

	POST /xled/v1/login HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	Content-Length: 61

	{"challenge": "AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8="}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 155
	Content-Type: application/json

	{"authentication_token":"5jPe+ONhwUY=","authentication_token_expires_in":14400,"challenge-response":"8d87f080947e343180da3f411df3997e3e9ae0cc","code":1000}


Verify
------

Verify the token retrieved by Login. Successful call invalidates previous token, if it existed.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/verify`

Parameters
``````````

Parameters as JSON object.

`challenge-response`
	(optional) value returned by login request.

Response
````````

The response will be an object.

`code`
	(integer), application return code.


Example
````````

Request::

	POST /xled/v1/verify HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 66

	{"challenge-response": "8d87f080947e343180da3f411df3997e3e9ae0cc"}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Logout
------

Probably invalidate access token. Doesn't work.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/logout`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
````````

Request::

	POST /xled/v1/logout HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 2

	{}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Device details
--------------

Gets information detailed information about the device.

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/gestalt`

Response
````````

The response will be an object.

For firmware family "D":

`product_name`
	(string) `Twinkly`
`product_version`
	(numeric string), e.g. "2"
`hardware_version`
	(numeric string), e.g. "6"
`bytes_per_led`
	(number), 4
`flash_size`
	(number), e.g. 16
`led_type`
	(number), e.g. 6
`led_version`
	(string) "1"
`product_code`
	(string), e.g. "TW105SEUP06"
`device_name`
	(string), name of the device - see section Device Name in Protocol details.
`rssi`
	(number), Received signal strength indication. Since firmware version: 2.1.0.
`uptime`
	(string) number as a string. Seconds since start. E.g. "60"
`hw_id`
	(string), see section Hardware ID in Protocol details.
`mac`
	(string) MAC address as six groups of two hexadecimal digits separated by colons (:).
`uuid`
	(string) UUID of the device. Since firmware version: 2.0.8. Device in family "D" has value 00000000-0000-0000-0000-000000000000.
`max_supported_led`
	(number), e.g. firmware family "D": 180 in firmware version 1.99.20, 224 in 1.99.24, 228 in 1.99.30, 255 in 2.0.0 and newer.
`base_leds_number`
	(number), e.g. 105
`number_of_led`
	(number), e.g. 105
`led_profile`
	(string) "RGB"
`frame_rate`
	(number), 25
`movie_capacity`
	(number), e.g. 719
`copyright`
	(string) "LEDWORKS 2017"
`code`
	(integer), application return code.

For firmware family "F" since firmware version 2.2.1:

`fw_family`
	(string) "F",
`product_name`
	(string) `Twinkly`
`hardware_version`
	(numeric string), "100"
`bytes_per_led`
	(number), 3
`flash_size`
	(number), 64
`led_type`
	(number), 14
`product_code`
	(string), e.g. "TWS250STP"
`device_name`
	(string), name of the device - see section Device Name in Protocol details.
`uptime`
	(string) number as a string. Miliseconds since start. E.g. "60000"
`hw_id`
	(string), see section Hardware ID in Protocol details.
`mac`
	(string) MAC address as six groups of two hexadecimal digits separated by colons (:). Address of a device in access point mode.
`uuid`
	(string) UUID of the device
`max_supported_led`
	(number), e.g. 510, since firmware version 2.4.14: 1020, since 2.4.22: 1200
`number_of_led`
	(number), e.g. 250
`led_profile`
	(string) "RGB"
`frame_rate`
	(number), e.g. 30.3, since firmware version 2.4.14: 17.86, since 2.4.16: 23.81, since 2.4.22: 25, since 2.4.30: 25.64
`movie_capacity`
	(number), e.g. 1984, since firmware version 2.4.14: 992
`copyright`
	(string) "LEDWORKS 2018"
`code`
	(integer), application return code.

For firmware family "G" since firmware version 2.4.21:

`fw_family`
	(string) "G",
`product_name`
	(string) `Twinkly`
`hardware_version`
	(numeric string), "100"
`flash_size`
	(number), 64
`led_type`
	(number), 12
`product_code`
	(string), e.g. "TWW210SPP" or "TWI190SPP"
`device_name`
	(string), name of the device - see section Device Name in Protocol details.
`uptime`
	(string) number as a string. Miliseconds since start. E.g. "60000"
`hw_id`
	(string), see section Hardware ID in Protocol details.
`mac`
	(string) MAC address as six groups of two hexadecimal digits separated by colons (:). Address of a device in access point mode.
`uuid`
	(string) UUID of the device
`max_supported_led`
	(number), e.g. 1200
`number_of_led`
	(number), e.g. 190 or 210
`led_profile`
	(string) "RGBW"
`frame_rate`
	(number), e.g. 28.57. Since firmware version 2.5.6: 24
`measured_frame_rate`
	(number), e.g. 27.78. Since firmware version 2.5.6.
`movie_capacity`
	(number), e.g. 992
`copyright`
	(string) "LEDWORKS 2018"
`wire_type`
    (integer), e.g. 1 or 4
`code`
	(integer), application return code.


Example
````````

Request::

	GET /xled/v1/gestalt HTTP/1.1
	Host: 192.168.4.1

Response from firmware family "D"::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 406
	Content-Type: application/json

	{"product_name":"Twinkly","product_version":"2","hardware_version":"6","flash_size":16,"led_type":6,"led_version":"1","product_code":"TW105SEUP06","device_name":"Twinkly_33AAFF","uptime":"60","hw_id":"0033aaff","mac":"5c:cf:7f:33:aa:ff","max_supported_led":224,"base_leds_number":105,"number_of_led":105,"led_profile":"RGB","frame_rate":25,"movie_capacity":719,"copyright":"LEDWORKS 2017","code":1000}

Response from firmware family "G"::

	HTTP/1.1 200 OK
	Server: esp-httpd/0.5
	Transfer-Encoding: chunked
	Content-Type: application/json

	{"product_name":"Twinkly","hardware_version":"100","bytes_per_led":4,"hw_id":"1cc190","flash_size":64,"led_type":12,"product_code":"TWI190SPP","fw_family":"G","device_name":"Twinkly_1CC190","uptime":"8107194","mac":"98:f4:ab:1c:c1:90","uuid":"E103C5A3-3398-4B77-AE1A-9D8998A5EB62","max_supported_led":1200,"number_of_led":190,"led_profile":"RGBW","frame_rate":28.57,"movie_capacity":992,"wire_type":4,"copyright":"LEDWORKS 2018","code":1000}


Get device name
---------------

Gets device name

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/device_name`

Response
````````

The response will be an object.

`name`
	(string) Device name.

`code`
	(integer), application return code.

Example
````````

Request::

	GET /xled/v1/device_name HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 37
	Content-Type: application/json

	{"name":"Twinkly_33AAFF","code":1000}


Set device name
---------------

Sets device name

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/device_name`

Parameters
``````````

Parameters as JSON object.

`name`
	(string) Desired device name. At most 32 characters.

Response
````````

The response will be an object.

`code`
	(integer), application return code. `1103` if too long.


Example
````````

Request::

	POST /xled/v1/device_name HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 26

	{"name": "Twinkly_33AAFF"}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 37
	Content-Type: application/json

	{"name":"Twinkly_33AAFF","code":1000}


Echo
----

Responds with requested message.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/echo`

Parameters
``````````

Parameters must be an JSON object. There doesn't seem to be any requirement on a structure.

Response
````````

The response will be an object.

`code`
	(integer), application return code. Returns 1001 on error.
`json`
	(object), contents is the same as the request.

Example
````````

Request::

	POST /xled/v1/echo HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 23

	{"message": "Hello!"}}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 44
	Content-Type: application/json

	{"json":{"message":"Hello!"},"code":1000}


Get timer
---------

Gets time when lights should be turned on and time to turn them off.

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/timer`

Response
````````

The response will be an object.

`time_now`
	(integer) current time in seconds after midnight

`time_on`
	(number) time when to turn lights on in seconds after midnight. -1 if not set

`time_off`
	(number) time when to turn lights off in seconds after midnight. -1 if not set

`code`
	(integer), application return code. Since firmware version: 2.3.8.

Example
```````

Request::

	GET /xled/v1/timer HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 45
	Content-Type: application/json

	{"time_now":17083,"time_on":-1,"time_off":-1}

Set timer
---------

Sets time when lights should be turned on and time to turn them off.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/timer`

Parameters
``````````

Parameters as JSON object.

`time_now`
	(integer) current time in seconds after midnight

`time_on`
	(number) time when to turn lights on in seconds after midnight. -1 if not set

`time_off`
	(number) time when to turn lights off in seconds after midnight. -1 if not set

Example
````````

Request to set current time to 2:00 AM, turn on lights at 1:00 AM and turn off at 4:00 AM::

	POST /xled/v1/timer HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 51

	{"time_now": 120, "time_on": 60, "time_off": 240}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Get LED operation mode
-------------------------

Gets current LED operation mode.

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/led/mode`

Response
````````

The response will be an object.

`code`
	(integer), application return code.
`mode`
	(string) mode of operation.
`shop_mode`
	(integer), by default 0. Since firmware version 2.4.21.

Mode can be one of:

* `off` - lights are turned off
* `demo` - in demo mode
* `movie` - plays predefined or uploaded effect
* `rt` - receive effect in real time

Example
```````

Request::

	GET /xled/v1/led/mode HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response::

	HTTP/1.1 200 OK
	Connection: close
	Content-Length: 28
	Content-Type: application/json

	{"mode":"movie","code":1000}

Set LED operation mode
----------------------

Changes LED operation mode.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/led/mode`

Parameters
``````````

Parameters as JSON object.

`mode`
	(string) mode of operation.
`effect_id`
	(int), id of effect, e.g. 0. Set together with `mode: effect`.

Mode can be one of:

* `off` - turns off lights
* `demo` - starts predefined sequence of effects that are changed after few seconds
* `movie` - plays predefined or uploaded effect. If movie hasn't been set (yet) code 1104 is returned.
* `rt` - receive effect in real time
* `effect` - plays effect with `effect_id`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
````````

Request::

	POST /xled/v1/led/mode HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 15

	{"mode":"demo"}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Get LED effects
---------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/led/effects`

Response
````````

The response will be an object.

`code`
	(integer), application return code.
`effects_number`
	(integer), e.g. 5 until firmware version 2.4.30 and 15 since firmware version 2.5.6.
`unique_ids`
	(array), since firmware version 2.5.6.

Item of `unique_ids` array is a UUID string. Default values are "00000000-0000-0000-0000-000000000001" up until "00000000-0000-0000-0000-00000000000F".


Example
````````
Request::

	GET /xled/v1/led/effects HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=

Response::

    HTTP/1.1 200 Ok
    Connection: close
    Content-Length: 32
    Content-Type: application/json

    {"effects_number":5,"code":1000}


Get current LED effect
----------------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/led/effects/current`

Response
````````

The response will be an object.

`code`
	(integer), application return code.
`unique_id`
    (string), UUID. Since firmware version 2.5.6.
`effect_id`
	(integer), e.g. 0

Example
````````
Request::

	GET /xled/v1/led/effects/current HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=

Response::

    HTTP/1.1 200 Ok
    Connection: close
    Content-Length: 27
    Content-Type: application/json

    {"effect_id":0,"code":1000}


Get LED config
--------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/led/config`

Response
````````

The response will be an object.

`strings`
	Array of objects
`code`
	(integer), application return code. Since firmware version: 1.99.20.

Item of strings array is object:

`first_led_id`
	(integer), e.g. 0
`length`
	(integer), e.g. 105

Example
```````

Request::

	GET /xled/v1/led/config HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response from firmware family "D"::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 57
	Content-Type: application/json

	{"strings":[{"first_led_id":0,"length":105}],"code":1000}

Response from Icicle firmware family "G"::

	HTTP/1.1 200 OK
	Server: esp-httpd/0.5
	Transfer-Encoding: chunked
	Content-Type: application/json

	{"strings":[{"first_led_id":0,"length":95},{"first_led_id":95,"length":95}],"code":1000}

Set LED config
--------------

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/led/config`

Parameters
``````````

Parameters as JSON object.

`strings`
	Array of objects

Item of strings array is object:

`first_led_id`
	(integer), e.g. 0
`length`
	(integer), e.g. 105

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
```````

Request::

	POST /xled/v1/led/config HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Type: application/json
	Content-Length: 45

	{"strings":[{"first_led_id":0,"length":100}]}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Upload full movie
-----------------

Effect is received in body of the request with Content-Type application/octet-stream. If mode is `movie` it starts playing this effect.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/led/movie/full`

Response
````````

The response will be an object.

`code`
	(integer), application return code.
`frames_number`
	(integer) number of received frames


Get LED movie config
--------------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/led/movie/config`

Response
````````

The response will be an object.

`frame_delay`
	(integer)
`leds_number`
	(integer) seems to be total number of LEDs to use
`loop_type`
	(integer), e.g. 0
`frames_number`
	(integer)
`sync`
	(object)
`mic`
    (object), since firmware family "G" version 2.4.21 until 2.5.6 and firmware family "F" version 2.4.14.
`code`
	(integer), application return code.

Contents of object `sync`:

`mode`
	(string), e.g. "none"
`slave_id`
	(string), e.g. "". Since firmware version 2.5.6 not available (only if empty?).
`master_id`
	(string), e.g. "". Since firmware version 2.5.6 not available (only if empty?).
`compat_mode`
	(number), default 0. Since firmware version 2.5.6.

Contents of object `mic`:

`filters`
	array of objects
`brightness_depth`
    (integer)
`hue_depth`
    (integer)
`value_depth`
    (integer)
`saturation_depth`
    (integer)

Example
```````

Request::

	GET /xled/v1/led/movie/config HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response from firmware family "D"::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 134
	Content-Type: application/json

	{"frame_delay":40,"leds_number":105,"loop_type":0,"frames_number":325,"sync":{"mode":"none","slave_id":"","master_id":""},"code":1000}

Response from firmware family "G"::

	HTTP/1.1 200 OK
	Server: esp-httpd/0.5
	Transfer-Encoding: chunked
	Content-Type: application/json

	{"frame_delay":0,"leds_number":0,"loop_type":0,"frames_number":0,"sync":{"mode":"none","slave_id":"","master_id":""},"mic":{"filters":[],"brightness_depth":0,"hue_depth":0,"value_depth":0,"saturation_depth":0},"code":1000}

Set LED movie config
--------------------

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/led/movie/config`

Parameters
``````````

Parameters as JSON object.

`frame_delay`
	(integer) the delay in milliseconds between two consecutive frames. For *n* fps, this is *1000 / n*.

`leds_number`
	(integer) seems to be total number of LEDs to use

`frames_number`
	(integer)

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Get current brightness
----------------------

Gets the current brightness level.

* For devices with firmware family "D" since version 2.3.5.
* For devices with firmware family "F" since 2.4.2.
* For devices with firmware family "G" since version 2.4.21.

HTTP request
````````````

`GET /xled/v1/led/out/brightness`

Response
````````

The response will be an object.

`code`
	(integer), application return code.
`mode`
	(string) one of "enabled" or "disabled".
`value`
	(integer) brightness level in range of 0..255

Mode string displays if the dimming is applied. The led shines at full
brightness regardless of what value is set if the `mode` is `disabled`.
Brightness level value seems to represent percent so 0 is dark and maximum
meaningful value is 100. Greater values doesn't seem to have any effect.


Example
```````

Request::

	GET /xled/v1/led/out/brightness HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 37
	Content-Type: application/json

	{"value":"100,"mode":"enabled","code":1000}

Set brightness
--------------

Since firmware version: 2.3.5.

HTTP request
````````````

`POST /xled/v1/led/out/brightness`

Parameters
``````````

Parameters as JSON object.

`mode`:
	(string) one of "enabled", "disabled"
`type`:
	(string) always "A"
`value`:
	(integer) brightness level in range of 0..255

When `mode` is "disabled" no dimming is applied and the led works at full
brightness. It is not necessary to submit all the parameters, basically it
would work if only `value` or `mode` is supplied. `type` parameter can be
omitted, and the only value seen on the wire was "A". Brightness level value
seems to represent percent so 0 is dark and maximum meaningful value is 100.
Greater values doesn't seem to have any effect.

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
```````

Set the brightness level to 10%:

Request::

	POST /xled/v1/led/out/brightness HTTP/1.1
	Host: 192.168.4.1
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Type: application/json
	Content-Length: 45

	{"mode":"enabled","type": "A","value": "100"}


Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13

	{"code":1000}


Set LED driver parameters
-------------------------

Since firmware version 1.99.18.

HTTP request
````````````
`POST /xled/v1/led/driver_params`

Parameters
``````````

Parameters as JSON object.

`t0h`
	(integer)
`t0l`
    (integer)
`t1h`
    (integer)
`t1l`
    (integer)
`tendh`
    (integer)
`tendl`
    (integer)

Response
````````

The response will be an object.

`code`
	(integer), application return code


Reset LED
---------

HTTP request
````````````
`GET /xled/v1/led/reset`

Response
````````

The response will be an object.

`code`
	(integer), application return code.


Reset2 LED
----------

Maybe reboot?

HTTP request
````````````
`GET /xled/v1/led/reset2`

Response
````````

The response will be an object.

`code`
	(integer), application return code.


Get firmware version
--------------------

Note: no authentication needed.

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/fw/version`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

`version`
	(string)

Example
````````

Request::

	GET /xled/v1/fw/version HTTP/1.1
	Host: 192.168.4.1
	Accept: */*

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 33
	Content-Type: application/json

	{"version":"1.99.24","code":1000}


Update firmware
---------------

Initiates firmware update.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/fw/update`

Parameters
``````````

Parameters as JSON object.

`checksum`
	(object)

Checksum object parameters for generation I devices:

`stage0_sha1sum`
	(string) SHA1 digest of first stage

`stage1_sha1sum`
	(string) SHA1 digest of second stage

Checksum object parameters for generation II devices:

`stage0_sha1sum`
	(string) SHA1 digest of first stage

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
````````

Request for generation I device::

	POST /xled/v1/fw/update HTTP/1.1
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Type: application/json
	Content-Length: 134
	Host: 192.168.4.1

	{"checksum":{"stage0_sha1sum":"1c705292285a1a5b8558f7b39abd22c5550606b5","stage1_sha1sum":"ac691b8d4563dcdbb3f837bf3db2ebf56fe77fbe"}}

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 13
	Content-Type: application/json

	{"code":1000}


Upload first stage of firmware
------------------------------

First stage of firmware is uploaded in body of the request with Content-Type application/octet-stream.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/fw/0/update`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

`sha1sum`
	SHA1 digest of uploaded firmware.


Upload second stage of firmware
-------------------------------

Second stage of firmware is uploaded in body of the request with Content-Type application/octet-stream.

Since firmware version 1.99.18.

Used only for generation I devices.

HTTP request
````````````

`POST /xled/v1/fw/1/update`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

`sha1sum`
	SHA1 digest of uploaded firmware.


Initiate WiFi network scan
--------------------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/network/scan`

Response
````````

The response will be an object.

`code`
	(integer), application return code.


Get results of WiFi network scan
--------------------------------

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/network/scan_results`

Response
````````

The response will be an object.

`code`
	(integer), application return code.

`networks`
	Array of objects

Item of networks array is object:

`ssid`
	(string)

`mac`
	(string)

`rssi`
	(number) negative number

`channel`
	(integer)

`enc`
	One of numbers 0 (Open), 1 (WEP), 2 (WPA-PSK), 3 (WPA2-PSK), 4 (WPA-PSK + WPA2-PSK), 5 (WPA2-EAP).


Response seems to correspond with `AT command CWLAP <https://github.com/espressif/ESP8266_AT/wiki/CWLAP>`_.


Get network status
------------------

Gets network mode operation.

Since firmware version 1.99.18.

HTTP request
````````````

`GET /xled/v1/network/status`

Response
````````
The response will be an object.

`mode`
	(enum) 1 or 2
`station`
	(object)
`ap`
	(object)
`code`
	(integer), application return code.

Contents of object `station` for firmware family "D":

`ssid`
	(string), SSID of a WiFi network to connect to
`ip`
	(string), IP address of the device
`gw`
	(string), IP address of the gateway
`mask`
	(string), subnet mask
`status`
	(integer), status of the network connection: 5 = connected, 255 = AP is used

Contents of object `station` for firmware family "G" since firmware version 2.4.21 and "F" since 2.2.1:

`ssid`
	(string), SSID of a WiFi network to connect to. If empty string is passed it defaults to prefix `ESP_` instead of `Twinkly_`.
`ip`
	(string), IP address of the device
`gw`
	(string), IP address of the gateway
`mask`
	(string), subnet mask

Contents of object `ap`:

`ssid`
	(string), SSID of the device
`channel`
	(integer), channel number
`ip`
	(string), IP address
`enc`
	(enum), 0 for no encryption, 2 for WPA1, 3 for WPA2, 4 for WPA1+WPA2
`ssid_hidden`
	(integer), default 0. Since firmware version 2.4.25.
`max_connection`
	(integer), default 4. Since firmware version 2.4.25.
`password_changed`
	(integer), either hidden or set to 1 if default password for AP was changed.

Example
````````

Request::

	GET /xled/v1/network/status HTTP/1.1
	Host: 192.168.1.2
	X-Auth-Token: 5jPe+ONhwUY=

Response::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 187
	Content-Type: application/json

	{"mode":1,"station":{"ssid":"home","ip":"192.168.1.2","gw":"192.168.1.1","mask":"255.255.255.0","status":5},"ap":{"ssid":"Twinkly_33AAFF","channel":11,"ip":"0.0.0.0","enc":0},"code":1000}


Set network status
------------------

Sets network mode operation.

Since firmware version 1.99.18.

HTTP request
````````````

`POST /xled/v1/network/status`

Parameters
``````````

Parameters as JSON object.

`mode`
	(enum), required: 1 or 2
`station`
	(object) optional, if mode set to 1 this parameter could provide additional details.
`ap`
	(object) optional, if mode set to 2 this parameter could provide additional details.


`station` object parameters:

`dhcp`
	(integer) 1

`ssid`
	(string) SSID of a WiFi network until firmare version 2.4.25

`encssid`
	(string) encrypted SSID of a WiFi network since firmare version 2.4.30.

`encpassword`
	(string) encrypted password.


`ap` object parameters:

`ssid`
	(string), required SSID of a WiFi network

`encpassword`
	(string), optional encrypted password.

`password`
    (string), optional plaintext password. Since firmware version 2.5.25 (?).

`enc`
	(enum), optional type of encryption. See above in Get network status. Defaults to 0 if not part of the request. If a request has `enc` value 1, get will return 0 as well.

`channel`
	(integer), optional

`ssid_hidden`
	(integer), optional, 0 to broadcast SSID, 1 to hide. Since firmware version 2.4.25.

`max_connection`
	(integer), optional, value from 1 to 4. Since firmware version 2.4.25.

Response
````````

The response will be an object.

`code`
	(integer), application return code.

Example
````````

Request to change network mode to client and connect to SSID "home" with password "Twinkly". Encoded with MAC address '5C:CF:7F:33:AA:FF'::

	POST /xled/v1/network/status HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 150

	{"mode":1,"station":{"ssid":"home","encpassword":"e4XXiiUhg4J1FnJEfUQ0BhIji2HGVk1NHU5vGCHfyclFdX6R8Nd9BSXVKS5nj2FXGU6SWv9CIzztfAvGgTGLUw==","dhcp":1}}

Request to change network mode to AP::

	POST /xled/v1/network/status HTTP/1.1
	Host: 192.168.1.100
	Content-Type: application/json
	X-Auth-Token: 5jPe+ONhwUY=
	Content-Length: 10

	{"mode":2}


Get MQTT configuration
----------------------

* For devices with firmware family "D" since version 2.0.22.
* For devices with firmware family "F" since version 2.4.2.
* For devices with firmware family "G" since version 2.4.21.

HTTP request
````````````
`GET /xled/v1/mqtt/config`

Response
````````

The response will be an object.

For firmware family "D":

`code`
	(integer), application return code.

`broker_host`
	(string), hostname of broker. By default `mqtt.twinkly.com`.

`broker_port`
	(integer), destination port of broker. By default "1883".

`client_id`
	(string), see section MQTT Client ID in Protocol details.

`encryption_key_set`
	(bool), by default "False"

`keep_alive_interval`
	(integer), by default "180".

`user`
	(string), by default "twinkly_noauth"

For firmware family "G" since firmware version 2.4.21 and "F" since 2.4.2:

`code`
	(integer), application return code.

`broker_host`
	(string), hostname of broker. By default `mqtt.twinkly.com`.

`broker_port`
	(integer), destination port of broker. By default "8883".

`client_id`
	(string), see section MQTT Client ID in Protocol details.

`keep_alive_interval`
	(integer), by default "60".

`user`
	(string), by default "twinkly32"

`password`
    (string), only in firmware family "F" since 2.4.2 until 2.4.14.

Example
````````

Request::

	GET /xled/v1/mqtt/config HTTP/1.1
	Host: 192.168.4.1
	Content-Type: application/json
	X-Auth-Token: mfqEJHHKJR8=

Response from firmware family "D"::

	HTTP/1.1 200 Ok
	Connection: close
	Content-Length: 169
	Content-Type: application/json

	{"broker_host":"mqtt.twinkly.com","broker_port":1883,"client_id":"5CCF7F33AAFF","user":"twinkly_noauth","keep_alive_interval":180,"encryption_key_set":false,"code":1000}

Response from firmware family "G"::

	HTTP/1.1 200 OK
	Server: esp-httpd/0.5
	Transfer-Encoding: chunked
	Content-Type: application/json

	{"broker_host":"mqtt.twinkly.com","broker_port":8883,"client_id":"98F4AB1CC190","user":"twinkly32","keep_alive_interval":60,"code":1000}


Set MQTT configuration
----------------------

Since firmware version: 2.0.22

HTTP request
````````````
`POST /xled/v1/mqtt/config`

Parameters
``````````

Parameters as JSON object.

For firmware family "D" since firmware version 2.0.22 and firmware family "G" since firmware version 2.4.21 and firmware family "F" since version 2.4.2:

`broker_host`
	(string), optional hostname of a broker

`client_id`
	(string), optional

`keep_alive_interval`
	(integer), optional

`user`
	(string), optional

Response
````````

The response will be an object.

`code`
	(integer), application return code.
