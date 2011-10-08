# Robopoly wireless network

This document describes the principles for implementing a network of low-powered simple devices for various input/output functions

## Protocol

Since we have existing XBee devices, thos will be used for the initial
deployment of the network. Each device should be accessible from a gateway
(linux server), possibly as a virtual apache host, that responds to RESTful
commands. e.g.
  `POST http://server/devices/output/ledmatrix1 {text="Leon is gay hurr durr derp"}`
or
  `GET http://server/devices/sensors/ambient1?unit=lux`
  `GET http://server/devices/sensors/temperature1?unit=C`

The gateway will provide the apache part, probably using python (Flask) with a
library to map the functions to the ZigBee end devices. Error checking etc is
provided by the python lib.

In the future, devices operating over 6LoWPAN will be developed by me, and
additional devices will use that protocol. It will probably be best to keep the
RESTful API, but it might also be interesting to be able to forward packets over
IPv6 to do direct TCP or UDP communication.

## API

The devices will need to respond to a similar command set. Communication should
be fairly simple (simple text packets over XBee, but using API mode), UDP in the
case of an IP network. Some basic CRCs can be included, and verified
server-side.

e.g.
SERVER				DEVICE
	GET TEMP --->	
	<--- 22.5 degC CRC
[verify CRC]

	SET TEXT "Hello world trololol" CRC  -->
	[display "Hello world trololol"]

in this case, the delimiters would have to be very well chosen. (or characters
escaped)

## Basic devices

Some basic devices are planned for the beginning of the network, obviously
people are expected to develop more later on.

### Led Matrix

A matrix should be able to display some basic text in a specified colour.
Commands:
- Wipe screen
- Display text
- Set colour (for next text/char)
- Read current text
- Query capabilities (number of characters, lines, colours)

Support for video or images should be considered later.

### Temperature / Light sensor

An environmental sensor should be able to log periodic readings, and provide the
latest readings as well as the n most recent ones. It should be possible to
change the logging interval.
Commands:
- Query temperature (deg C)
- Query luminosity (lux)
- Query humidity
- Query capabilities (temperature, lum, humidity, etc)
- Set logging interval
- Ask for N latest readings

### Ambilight

Some led strips and/or lamps should be connected to the network and controllable
remotely. A lamp should be able to inform of its capabilities (colour or
colours, brightness levels (for each colour)

e.g. GET INFO -> "Colour:R,G,B;Levels:256" or "Colour:W;Levels:100"

might be nice to know if it's warm white or cold white.
