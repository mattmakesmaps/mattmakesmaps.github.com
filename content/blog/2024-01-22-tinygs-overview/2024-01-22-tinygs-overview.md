---
title: Build a TinyGS Cube Satellite Receiver
draft: false
description: Overview of the TinyGS project and home receiving station.
date: 2024-01-22
tags:
    - ham radio
    - tinygs
    - satellite
---

As an amateur radio enthusiast, I've long been interested in DIY reception of satellite signals. Previously, I've succeeded at pulling down live [NOAA weather satellite imagery](https://docs.google.com/presentation/d/e/2PACX-1vR-d59s4KdPILqCsMLZ8jk274in0BSCQZYdrsFT7-e8vsb0iXxtu3cfHsKewWCVdejAvG5Ju19OGkWD/pub?start=false&loop=false&delayms=3000&slide=id.p), and failed at retrieving NOAA GOES satellite full disk earth imagery.

At the end of 2023, I built a small cubesat telemetry receiver (or ground station) connected to the [TinyGS project](https://www.tinygs.com).

The result of my station build can be seen at my station's [homepage on the TinyGS web app](https://tinygs.com/station/KI7ZTH@6866355156). 

{% image "./ki7zth_polar_map.png", "Screenshot of recent telemetry packets from my backyard station." %}

Above, you can see the positions of the most recently received telemetry packets. 

Each packet of binary data received by my station (and the thousands of others globally) are published to the TinyGS backend MQTT infrastructure where they are decoded and stored in a separate backed (which I'm not familiar with). [MQTT](https://aws.amazon.com/what-is/mqtt/#:~:text=MQTT%20is%20a%20standards%2Dbased,constrained%20network%20with%20limited%20bandwidth.) is an IoT backend "broker" in a publish/subscribe model, in which my ground station acts as a publisher and other parts of the TinyGS storage infrastructure (presumably) are subscribers.

The magic of this project revolves around the LoRa communication protocol that allows for long distance, weak signal, communication from the cube satellites to terrestrial ground stations. LoRa is a [spread spectrum protocol](https://en.wikipedia.org/wiki/Spread_spectrum) (up to 500 kHz in bandwidth) that allows for signals to be extracted deep below the [noise floor](https://en.wikipedia.org/wiki/Noise_floor).

The video below provides a great "Hello World" on the protocol.

<iframe width="560" height="315" src="https://www.youtube.com/embed/dxYY097QNs0?si=OM7XgorcbyMACoJQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Building your own

I've iterated a few times on this station setup, and am pretty happy with its current configuration, as pictured below.

**TODO:** Picture

Essentially, the system is composed of a 1/4 wave ground plane antenna, an ESP32 board flashed with the TinyGS Firmware, wrapped up in a weatherproof box. Below you'll find notes on various parts of the construction.

### Antenna Construction

I already had a DIY quarter-wave ground plane antenna lying around, tuned for the 2m band. Since these antenna elements were longer then what is required for this project (around the 70cm band), all I needed to do was actually cut the elements to length. My antenna was made out of simple 14 gauge copper wire, but it could also be made out of aluminum rods which looks nicer and is probably more robust.

The following video gives a good overview on how to construct an antenna for this project. It's also where I cribbed the correct lengths to trim my antenna elements.

<iframe width="560" height="315" src="https://www.youtube.com/embed/6cVYsHCLKq8?si=YO6IH8GMQ-eP4Mba" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### Hardware and Software

The project centers around an ESP32 based LoRa board, of which there are many different implementations. The particular one I went with is called the [official TTGO Meshtastic T-Beam V1.1 ESP32 433/868/915/923Mhz WiFi Bluetooth ESP32 GPS NEO-6M SMA 18650 Battery Holder With OLED](https://www.aliexpress.us/item/2255801140943675.html). There are cheaper ones available, but I decided to use this one since it had the capability to run off of [18650 rechargable batteries](https://www.amazon.com/Rechargeable-Flashlights-Headlights-Surveillance-Equipment/dp/B0BN1ZVS5X/ref=sr_1_4?keywords=18650+battery&qid=1705962273&sr=8-4)

With the board in hand, installing the project software is as easy as following the [instructions on the TinyGS project page](https://github.com/G4lile0/tinyGS/wiki/Quick-Start).

### Enclosure and Power

I used the following parts to build the current version of the enclosure, which has survived well in the wet Seattle winter.

- [Enclosure - A small plastic box](https://www.amazon.com/dp/B07RC83LTZ)
- [Cable Glands - Provide weatherproof holes for cable routing](https://www.amazon.com/dp/B0CC898NH5)
- [Electrical Tape - to waterproof antenna connection](https://www.amazon.com/gp/product/B0000AZ9HG)
- [25 foot USB cable - for mains power)](https://www.amazon.com/dp/B07Z7SNH4T)

I switched to mains power via USB since the 18650 batteries only lasted about 3-4 hours per charge. I'd avoid using these batteries unless the small runtime works for your needs.

Additionally, I had to mount the antenna to the box somehow. I first used a small piece of scrap wood, but later glued together small strips of plastic to form a vertical antenna mount. Any scraps will do.

As you can see in the picture below, you can really just get creative with what's lying around. Here is a previous setup using a tupperware container and some scrap wood... setup in the University of Washington's Red Square.

{% image "./IMG_1580.png", "Screenshot of recent telemetry packets from my backyard station." %}

## Conclusions

There are so many interesting aspects of this project to hack on. Some of which include.

- Exploring different antenna configurations. Compare your results to those of [your neighbors](https://tinygs.com/station/W7RCS@6500205772)!
- Figuring out how raw binary packets are [decoded via the tinygs-decoders project](https://github.com/4m1g0/tinygs-decoders/tree/master).
- Building [your own MQTT workflow](https://www.youtube.com/watch?v=jwsSpdWuTwk) to preserve packets that your station has received.