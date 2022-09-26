==========================
Smart LED Christmas lights
==========================

`Unofficial documentation`_ of :ref:`API reference <api-reference>`, protocol
details and hardware of `Twinkly`_ - Smart Decoration LED lights for Christmas.

Official materials says:

    Twinkly is a LED light device that you can control via smartphone. It
    allows you to play with colouful and animated effects, or create new ones.
    Decoration lights, not suitable for household illumination.

Since its `Kickstarter project`_ in 2016 many products were introduced with
varying properties and features. Most notably products released since September
2019 are identified as Generation II. Older products are since then referred as
Generation I. Documentation has been created and tested only on some
:ref:`hardware <hardware>`.

Products could be further groupped by families for which firmware is released.
Firmware on devices can be upgraded and sometimes new features are introduced.
Documentation has been created for some of these :ref:`firmwares <firmware>`.

Why?
----

My first Twinkly was 105 LEDs starter light set. That was the latest available
model in 2017: TW105S-EU. As of December 2017 there are only two ways to
control lights: mobile app on Android or iOS or hardware button on the cord.

Android application didn't work as advertised on my Xiaomi Redmi 3S phone. On
first start it connected and disconnected in very fast pace (like every 1-2
seconds) to the hardware. I wasn't able to control anything at all. Later I
wanted to connect it to my local WiFi network. But popup dialog that shouldn't
have appear never did so.

Public API was `promised around Christmas 2016`_ for next season. Later update
from October 2016 it seems `API won't be available any time soon`_:

    API for external control are on our dev check list, we definitely need some
    feedback from the community to understand which could be a proper core set
    to start with.

It turned out that application uses HTTP to control lights. I ended up with
capturing network traffic and documented this private API. In the end I'm able
to configure the device pretty easilly.

As of 2020 Twinkly devices can be controlled by Amazon Alexa and Google
Assistant as well. Mobile application now requires an account to operate lights
even locally. No sign of public API for local devices though. Therefore with my
second device - Twinkly 210 RGB+W Wall I keep updating this documentation to be
able to operate my devices locally and not rely on availability of
manufacturer's servers.

License
-------

Documentation is available under MIT license.

Client implementations
----------------------

There are multiple clients, libraries or just small projects that can be used
to control Twinkly devices:

* `xled`_  - python library and command line interface
* `xled_plus`_ - Addons to the XLED package, to create nice effects for Twinkly LED lights
* `Twinkly integration in Home Assistant`_
* SmartThings:

  * `Twinkly integration in SmartThings by StevenJonSmith`_
  * `Twinkly integration in SmartThings by Dameon87`_

* `TwinklyTree Binding`_ for openHAB
* `Twinkly HomeKit Hub for Mongoose OS`_ using `Twinkly library for Mongoose OS`_
* `homebridge-twinkly` - unofficial Homebridge plugin
* `TwinklyWPF`_ - .net 5 GUI and API library
* `ioBroker.twinkly`_ - twinkly adapter for ioBroker to communicate with the Twinkly lights
* `Twinkly Twinkly Little Star` - ttls helps you to make async requests to Twinkly LEDs. Includes CLI and some examples how to create both loadable movies and realtime sequences.
* `Twinkly.vb for HomeSeer`_
* `bpx`_ - BiblioPixel driver for the xled library
* `thingzi-logic-twinkly`_ - Twinkly lights integration for node red
* `twinkly-animator`_ - Drawing and animation tools for Twinkly LED lights
* Python class to interact with generation I device and IDA Pro loader of firmware binary in `Twinkly Twinkly Little Star by F-Secure LABS`_.
* `Artnet 2 Twinkly`_ - interface XLights to Twinkly on a PC. Partially emulates and artnet controller, and even hooks into Twinkly's web api to allow manual layout editing.

Communities
-----------

There are multiple places where you can connect with others who are interested
in Twinkly devices:

* `Twinkly (unofficial users group) on Facebook`_ to share your installations, show your creations, ask questions, post some hacks. Anything Twinkly related goes.
* `Discussions on GitHub in xled_plus project`_ with daily effects.
* `Gitter chat xled-community`_ of `xled` project. Fairly small and quiet.

.. _`Twinkly`: https://www.twinkly.com/
.. _`Unofficial documentation`: https://xled-docs.readthedocs.io/
.. _`Kickstarter project`: https://www.kickstarter.com/projects/twinkly/twinkly-smart-decoration-for-your-christmas
.. _`promised around Christmas 2016`: https://www.kickstarter.com/projects/twinkly/twinkly-smart-decoration-for-your-christmas/comments?cursor=15497325#comment-15497324
.. _`API won't be available any time soon`: https://www.kickstarter.com/projects/twinkly/twinkly-smart-decoration-for-your-christmas/comments?cursor=14619713#comment-14619712
.. _`xled`: https://pypi.org/project/xled/
.. _`xled_plus`: https://pypi.org/project/xled-plus/
.. _`Twinkly integration in Home Assistant`: https://www.home-assistant.io/integrations/twinkly/
.. _`Twinkly integration in SmartThings by StevenJonSmith`: https://github.com/StevenJonSmith/SmartThings
.. _`Twinkly integration in SmartThings by Dameon87`: https://github.com/Dameon87/SmartThings
.. _`TwinklyTree Binding`: https://github.com/mvanhulsentop/openhab-addons/tree/twinklytree/bundles/org.openhab.binding.twinklytree
.. _`Twinkly library for Mongoose OS`: https://github.com/d4rkmen/twinkly
.. _`Twinkly HomeKit Hub for Mongoose OS`: https://github.com/d4rkmen/twinkly-homekit
.. _`homebridge-twinkly`:  https://github.com/nschum/homebridge-twinkly
.. _`TwinklyWPF`: https://github.com/MarkAlanJones/TwinklyWPF
.. _`ioBroker.twinkly`: https://www.npmjs.com/package/iobroker.twinkly
.. _`Twinkly Twinkly Little Star`: https://github.com/jschlyter/ttls
.. _`Twinkly.vb for HomeSeer`: https://forums.homeseer.com/forum/developer-support/scripts-plug-ins-development-and-libraries/script-plug-in-library/1348314-twinkly-vb-christmas-tree-lights-with-predefined-and-custom-animations
.. _`bpx`: https://github.com/rec/bpx
.. _`thingzi-logic-twinkly`: https://www.npmjs.com/package/thingzi-logic-twinkly
.. _`twinkly-animator`: https://github.com/sluggoman/twinkly-animator
.. _`Twinkly Twinkly Little Star by F-Secure LABS`: https://labs.f-secure.com/blog/twinkly-twinkly-little-star/
.. _`Gitter chat xled-community`: https://gitter.im/xled-community/chat
.. _`Discussions on GitHub in xled_plus project`: https://github.com/Anders-Holst/xled_plus/discussions
.. _`Twinkly (unofficial users group) on Facebook`: https://www.facebook.com/groups/twinklyusergroup
.. _`Artnet 2 Twinkly`: http://www.artnet2twinkly.com/
