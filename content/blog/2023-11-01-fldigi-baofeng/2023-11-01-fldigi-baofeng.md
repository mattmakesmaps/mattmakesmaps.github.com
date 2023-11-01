---
title: FLDIGI on Mac With Digirig and Baofeng HT
draft: false
description: Setup of fldigi software with digirig and baofeng ht
date: 2023-11-01
tags: ham radio
---

I participated for the first time in the weekly [Puget Sound Digital Ham Net](https://www.psdhn.org/)
this past Monday. This net specializes in sending digital data over the 2m and 70cm amateur radio bands
as opposed to traditional voice communication. This allows for text and image files to be sent over
a variety of different "digital modes", although the most common for the group appeared to be
[multi-frequency shift keyed](http://www.w1hkj.com/modes/mfsk.htm) aka `MFSK128`,
where the 128 indicates 128 baud or symbols per second. Pretty amazing to be able to send data across
Seattle without the internet.

With the help of [Ryan KB7SDM](https://www.qrz.com/db/KB7SDM) and a great
[getting started presentation](https://www.youtube.com/watch?v=zSX64XwJ0E4)
a group member had put together, I was able to receive and decode other participants' messages
using the [`fldigi` software suite](http://www.w1hkj.com/).

Here is an example of a message transmitted by a net member, received by my radio, and decoded on my
macbook pro using `fldigi`.

{% image "./fldigi-receive.png", "Screenshot of fldigi software displaying decoded message." %}

Receiving was as easy as tuning my radio to the net frequency, and turning on my mac's microphone to
capture the sound from the radio's speaker. With regards to transmitting however, I wanted to be able
to connect the radio to directly the computer. This ensures maximum fidelity in the transmission,
and also allows the software to directly control the radio, "keying" or opening and closing the radio
for transmit only while sending a message. Alternatively, I'd have to manually key the radio myself.

In comes the [digirig](https://digirig.net/) a tiny piece of hardware that sits between the
radio and computer, and essentially acts as a usb soundcard enables software control of the radio
itself.

Below you can see my handheld radio, a baofeng UV-5R, and the digirig interface. The digirig is connected
to the radio via an audio cable that plugs into both the radio's speaker and microphone jacks, allowing
for transmit and receive into the macbook pro. The digirig itself is connected to the computer via usb-c.

{% image "./IMG_0758.jpeg", "A baofeng uv-5r handheld ham radio connected to a digirig interface." %}

Actually configuring the `fldigi` software to key the radio via the digirig was a little tricky.
[This post](https://forum.digirig.net/t/digirig-flrig-baofeng/580/4) on the digirig forum
helped clear it up, however.

I had to set my `fldigi` software's `Hardware PTT` controls to the following (open image in a new tab
to enlarge):

{% image "./rig_control.png", "fldigi configuration menu for baofeng use." %}

Basically the magic here is:
- Check `Use separate serial port PTT`
- Check `Use RTS`
- Set `Device` to `/dev/cu.usbserial-1120`

The first setting, `Use separate serial port PTT` I believe enables `fldigi` to use the new serial port
which the digirig hardware creates.

The second setting, `Use RTS` tells `fldigi` to transmit a `Ready to Transmit Signal` to the radio, which
actually keys up the radio and begins the transmission.

The third setting, for the device, simply selects the digirig from the list of connected devices on
the mac. There are two varients listed for each device, with `cu` or `tty` prefixes.
e.g `/dev/cu.usbserial-1120` or `/dev/tty.usbserial-1120`. This [stack overflow post](https://stackoverflow.com/questions/8632586/whats-the-difference-between-dev-tty-and-dev-cu-on-macos) 
explains the difference between the two. `cu` appears to be used for outbound connections on linux
systems.

I was able to successfully transmit over simplex with this setup, and am looking forward to trying
out more modes in a future iteration of the Puget Sound Digital Hams Net.