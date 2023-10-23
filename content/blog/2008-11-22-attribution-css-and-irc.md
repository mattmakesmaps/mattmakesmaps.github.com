---
date: 2008-11-22
title: Attribution, CSS, and IRC
---

I've built up the [WFS OpenLayers example](http://www.mkgeomatics.com/apps/openlayers/WA_WFS_WGS84_query.html) I've been working on to now include attribution (by hovering over a feature) as well as a pointless select feature capability. Click away! It won't do anything but, that's fine. <!-- more -->

A different baselayer is also available, [openstreetmaps.org](http://www.openstreetmap.org/) (via telascience.org tiles). You may notice that some of these tiles fail to reload upon panning and zooming operations. I asked why this was on the OpenLayers IRC, and was told that these tiles basically suck. I'm stuck using them however, as they are Lat/Long WGS84. OSM Mapnik and Google maps require a spherical mercator projection, and as such PROJ.4 needs to be amended to support this EPSG code so that Mapserver and render properly.

[caption id="attachment_121" align="alignnone" width="300" caption="Screenshot of simple WFS example"][![Screenshot of simple WFS example](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/wfs_example-300x276.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/wfs_example.jpeg)[/caption]

It [looks pretty straightforward](http://docs.openlayers.org/spherical_mercator/#mapserver), but another project for another day.

In any event, I've kept the OpenLayers WMS as the primary base layer, which doesn't have display issues.
