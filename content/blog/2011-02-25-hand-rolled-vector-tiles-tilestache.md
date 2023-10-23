---
date: 2011-02-25
title: Hand-Rolled Vector Tiles - TileStache
---

A few weeks ago I found myself surfing the intertubes for instructions on how to serve up some vector tile goodness. That search came up pretty much empty, except for one glimmering [thread](http://gis.stackexchange.com/questions/3712/create-vector-geojson-tiles-for-polymaps) of hope. The answer, [TileStache](http://tilestache.org/) { <-- Imagine that's a mustache on it's side.


> TileStache is a Python-based server application that can serve up map tiles based on rendered geographic data.

<!-- more -->

By design, TileStache can be used to serve up stylish TMS tiles using [mapnik](http://mapnik.org/) map files, and can also be used to locally cache remote-services via [proxy](http://tilestache.org/doc/#providers). What I'm most interested in though, is it's ability to deploy vector tiles. So what are vector tiles? Think TMS tiles... but replace representations of the geometries through images, with [GeoJSON](http://geojson.org/). Pretty wild right? Specifically, the TileStache [PostGeoJSON Provider](http://tilestache.org/doc/TileStache.Goodies.Providers.PostGeoJSON.html) can be used to connect TileStache to a PostGIS data source, and return a tile comprised entirely of GeoJSON data.

For example, data from a PostGIS data source can be rendered as an image tile (.../10/16/357.png), like this:

[![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/tile.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/tile.png)

But can also be represtented as a vector tile (.../10/16/357.json), like this:

```javascript
// Subset of a single 256x256 pixel vector tile.
{
  "type": "FeatureCollection",
  "features": [
    {
      "geometry": {
        "type": "MultiPolygon",
        "coordinates": [
          [
            [
              [
                -122.973093,
                47.969842
              ],...
              [
                -122.973093,
                47.969842
              ]
            ]
          ]
        ]
      },
      "type": "Feature",
      "properties": {
        "property_s": "USFS",
        "juris_name": "Olympic National Forest"
      },
      "id": 1280
    }
  ]
}
```

So what are the advantages of using vector tiles? You can already use [OpenLayers' GeoJSON](http://dev.openlayers.org/docs/files/OpenLayers/Format/GeoJSON-js.html) format reader to populate a vector layer in OL. It's an issue of size. Highly complex geometries can be large in size, and requesting all that data at once can be time consuming. Vector tiles approach this problem using the same answer as TMS... only request those sections of data which you need at that time. By only requesting those tiles within the user's current extent + a small buffer, the need to download large geometries at once can be negated. Furthermore, just as TMS's can be pre-cached to disk (seeded), so can vector tiles.

One example of this is serving up a combined NFS boundary dataset compiled by my good pal, Greg ([http://www.chopshopgeo.com/blog/](http://www.chopshopgeo.com/blog/)). These boundaries are **dense** and displaying them at their full extent & raw level of detail is expensive. But by breaking the vector representations of these geometries up into a standard tile scheme, only those tiles which we need are requested, and only when we need them. As a side note, in addition to tiling, I also simplified the boundaries, to promote faster load time at small-scales. The least granular vector representations display at the smallest zoom-scales, while the highest (raw, unsimplified) level of granularity displays only at the largest zoom-scales.

[caption id="attachment_398" align="alignnone" width="665" caption="NFS Boundaries Provided By ChopShopGeo"][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/parks.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/parks.png)[/caption]

Additionally, using vector representations of geometry rather then cached images allows styling of those geometries on the fly. [Polymaps](http://polymaps.org/), the only display client I've found so far that can consume vector tiles out-of-the-box, renders these tiles as SVG elements. Because of this, unique styling can be applied via CSS; controlling the color, stroke, fill, etc. of each geometry in response to both attributes associated with the geometry (see image below) or user input... ala the [Polymaps example page](http://polymaps.org/ex/statehood.html).

[caption id="attachment_393" align="alignnone" width="691" caption="USGS real-time gauge stations. Darker dots represent stronger streamflow, lighter dots represent slower flow. You'll have to ignore the fact that I'm symbolizing streamflow without the streams."][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/usgs.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/02/usgs.png)[/caption]

The above example converts data from the [USGS Instantaneous Values Web Service](http://waterservices.usgs.gov/rest/WOF-IV-Service.html) (part of the [USGS Water Date for the Nation program](http://waterdata.usgs.gov/nwis/)) as a JSON response to GeoJSON. These data points are then symbolized dynamically using Polymaps. More on that later.


```javascript
{
"type": "FeatureCollection",
"features": [{
"geometry": {
"type": "MultiPolygon",
"coordinates": [
[
[
[-122.973093, 47.969842],
[-122.973093, 47.969842]
]
]
]
},
http: //jsbeautifier.org/
"type": "Feature",
"properties": {
"property_s": "USFS",
"juris_name": "Olympic National Forest"
},
"id": 1280
}]
}
```
