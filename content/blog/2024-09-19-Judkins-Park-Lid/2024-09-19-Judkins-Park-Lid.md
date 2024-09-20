---
title: Judkins Park I-90 Before "The Lid"
draft: true
description: Notes on the street configuration around Judkins Park before the I-90 Lid
date: 2024-09-16
tags:
    - GIS
    - Seattle
---

## Background

I've lived in the Judkins Park neighborhood of Seattle for the last ten years. 

The City's own [Neighborhood Map Atlas](https://data-seattlecitygis.opendata.arcgis.com/datasets/b4a142f592e94d39a3bf787f3c112c1d/explore?location=47.585811%2C-122.294092%2C14.47) lists the primary name of this neighborhood as `Atlantic` with `Judkins Park` as an alternative name among `Colman` and `Jackson Place`. I've also thought of this neighborhood as the south end of the Central District.

Interstate 90 runs beneath this neighborhood, encapsulated for a length of 3,300 feet according to the
Mount Baker Tunnel [wikipedia page](https://en.wikipedia.org/wiki/Mount_Baker_Tunnel). The eastern half
of that tunnel has existed since 1940 (according to the previously linked wikipedia article). The western
half however was exposed, and covered as part of the creation of a "lid" that developed Sam Smith Park,
Jimi Hendrix Park, and the opening of the (potentially soon to be shuttered)
Thurgood Marshall Elementary School.

**I-90 West and East Tunnel Entrances.**

Note the layout of I-90 in the image below. The West and East tunnel entrances can be seen on either
side. `23rd Ave S` is visible as the western most street running North/South. 
`Martin Luther King Jr Way S` (Formerly `Empire Way`) is visible bisecting Sam Smith Park to the East
and Thurgood Marshall Elementary/Jimi Hendrix Park/Northwest African American Museum to the West.

The area in red indicates the rough location of The Lid, as seen in higher detail below.

{% image "./Park_Wide.png", "I-90 West and East Tunnel Entrances." %}

Image Credit: [King County 2023](https://gismaps.kingcounty.gov/arcgis/rest/services/BaseMaps/KingCo_Aerial_2023/MapServer)

**I-90 Lid: Jimi Hendrix Park/Northwest African American Museum - Sam Smith Park**

{% image "./Park_Closeup.png", "I-90 Lid: Sam Smith Park - Jimi Hendrix Park." %}

Image Credit: [King County 2023](https://gismaps.kingcounty.gov/arcgis/rest/services/BaseMaps/KingCo_Aerial_2023/MapServer)

As I've walked and cycled across this green space, I've tried to imagine what this area was like
before The Lid existed. I have always assumed there had to be some combination of overpasses,
underpasses, onramps, offramps, and maybe even a sidewalk or two allowing the interstate and a
neighborhood to co-exist somehow.

After a bit of research on the MOHAI image archives and a visit to the
[Seattle Room](https://www.spl.org/programs-and-services/arts-and-culture/unique-collections/seattle-room) of the Central Library branch, I found out I was generally correct (I believe).

## Pre-Lid Condition

**1982: Former West Portal Mt. Baker Ridge Tunnel.**

This photo was my first clue, found on the Mount Baker Tunnel
[wikipedia page](https://en.wikipedia.org/wiki/Mount_Baker_Tunnel). Looking East, it shows
the West tunnel entrance to I-90 at `Yakima Ave S.` Current day, this would be underground,
below the current day Bike/Pedestrian Tunnel of Sam Smith Park (See below)

{% image "./Former_west_portal_of_Mt._Baker_Ridge_Tunnel_in_1982.jpg", "Former West Portal Mt. Baker Ridge Tunnel 1982." %}

Image Credit: [By Steve Morgan, CC BYSA 4.0](https://commons.wikimedia.org/w/index.php?curid=69055887)

The current bike/pedestrian tunnel, opened in 1994, situated on The Lid above the 
former tunnel portals pictured previously.

{% image "./bike_tunnel.jpg", "Sam Smith Park Bike Tunnel." %}

Image Credit: [By Robert Ashworth, CC BYSA 2.0](https://commons.wikimedia.org/wiki/File:I-90_bike_tunnel,_Seattle_(10508441764).jpg)

Given the gentle slope of The Lid, it's hard to imagine being in the past, walking South from say
Norman Street, and running into an impassible mass of freeway. It's literally _hard to imagine_.

I looked for available aerial imagery from before the lid's construction, but couldn't really
find anything. So I decided to take a trip to the "Seattle Room" of the Central Library branch,
which has a collection of Kroll Street Atlases. I was able to find a copy from 1977, which
showed the old street configuration in perfect detail.

I took a few photos with my phone of the relevant sheets, georeferenced them, and created the
side-by-side map embedded below. Panning and zooming the map can allow you to compare the
1977 Pre-Lid layout with aerial imagery showing the current configuration. Note tha the
georeferencing is a best approximation given the fact that I was taking cell-phone shots
of these sheets.

<iframe src="https://mattmakesmaps.maps.arcgis.com/apps/instant/compare/index.html?appid=7f63f129f63f44c1b5e6afd95f89cbac" width="1000" height="600" frameborder="0" style="border:0" allowfullscreen>iFrames are not supported on this page.</iframe>

**1983: View of Atlantic Street neighborhood, Seattle.**

{% image "./imlsmohai_9876_full.jpg", "View of Atlantic Street neighborhood, Seattle, 1983." %}

Image Credit: [MOHAI, Seattle Post-Intelligencer Photograph Collection, 9876](https://digitalcollections.lib.washington.edu/digital/collection/imlsmohai/id/9876/rec/156)

**1954: View of Rainier Avenue and Lake Way looking east.**

{% image "./imlsmohai_17965_full.jpg", "View of Rainier Avenue and Lake Way looking east, March 1954." %}

Image Credit: [MOHAI, Seattle Post-Intelligencer Photograph Collection, 17965](https://digitalcollections.lib.washington.edu/digital/collection/imlsmohai/id/17965/rec/157)


## Background

- Data were collected at the Seattle Room of the Central Branch library.
- Data were georeferenced using Esri ArcGIS Pro, which was also used to create the web map above.