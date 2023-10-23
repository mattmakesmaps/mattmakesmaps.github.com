---
title: "MapBox + Github = Free and Fast Web Maps"
date: 2013-07-17
---

With the announcement of Github's awesome support for [direct geojson renderingg](http://www.mapbox.com/blog/github-mapbox-maps/),
folks can deploy an embeddable web map with a single git commit and push. This got me
thinking about the potential of github, specifically [github pages](http://pages.github.com/),
as a vehicle for the display of more customized web mapping applications. The result of that
experiment is [aknativenamesatlas.com](http://aknativenamesatlas.com). <!-- more -->

I deployed the initial draft of this site in a few hours, thanks entirely not only to 
the power of MapBox and Github's hosting infrastructure, but also to the easy-to-use
[mapping templates](http://www.mapbox.com/map-sites/) provided by MapBox.

This blog series will attempt to illustrate some of the major steps involved in the
process of deploying this map. The general process however, is as follows.

1. Style cartographic layers via TileMill, upload to MapBox.
2. Create composite map of individual operation layers plus a base within MapBox.
3. Fork MapBox's dc-properties-template via github.
4. Edit forked repository to point to my layers of interest.
5. Add CNAME record for custom URL to forked repository.
6. Configure DNS to route custom domain name to github pages.

This first post will cover the cartography, and more importantly, the on-the-fly compositing of
layers.

# Cartography & Compositing

The primary tool used to create the cartographic look and feel of the individual data layers is
[TileMill](http://www.mapbox.com/tilemill/). TileMill is a desktop cartographic design studio, allowing you to
load your own datasets from a variety of spatial formats (SHP, SpatialLite, PostGIS), and style via a CSS-like
language, [CartoCSS](http://www.mapbox.com/tilemill/docs/manual/carto/).

I'll gloss over the details for styling maps in in TileMill, but they've got a great
[quick-start guide](http://www.mapbox.com/tilemill/docs/crashcourse/introduction/).

For my site, I ended up creating two projects in TileMill, one for each operational layer present in the final
map. The first layer represents [place names](http://a.tiles.mapbox.com/v3/mattmakesmaps.language_points/page.html)
, symbolized as points. The second layer depicts [language regions](http://a.tiles.mapbox.com/v3/mattmakesmaps.language_lines/page.html)
, symbolized as lines, but the geometry are in actuality polygons (required for the display of attribute information via a hover event).

Splitting these operational layers into separate projects in TileMill, and conversely separate web maps when uploaded to MapBox, allows
us to mix and match these layers at will. Specifically, the [MapBox REST API](http://www.mapbox.com/developers/api/#Map.resources)
allows us to composite or layer individual maps into a single-tile, on-the-fly. If you're head hasn't been exploded, allow me to illustrate.

The map on [aknativenamesatlas.com](http://aknativenamesatlas.com) is a composite of multiple layers. A basemap, provided by mapbox, as well as two operational layers (a point layer and a line layer).

A [single tile](http://a.tiles.mapbox.com/v3/mattmakesmaps.map-9ipoh344/6/5/16.png) from this map looks like this:

```Archived Post. Original Image Unavailable.```

The URL is as follows: [http://a.tiles.mapbox.com/v3/mattmakesmaps.map-9ipoh344/6/5/16.png](http://a.tiles.mapbox.com/v3/mattmakesmaps.map-9ipoh344/6/5/16.png). Note the `mattmakesmaps.map-9ipoh3441` section of the URL. This is a combination of my user account, `mattmakesmaps` and the unique map id. This identifier indicates that I'm requesting the tile representing this specific map, again, a composite of multiple layers.

We can however, query individual layers that make up this map. For example, the lines layer specifically has a URL endpoint of [http://a.tiles.mapbox.com/v3/mattmakesmaps.language_lines/6/5/16.png](http://a.tiles.mapbox.com/v3/mattmakesmaps.language_lines/6/5/16.png). It looks like this.

```Archived Post. Original Image Unavailable.```

Similarly, with the points layer: [http://a.tiles.mapbox.com/v3/mattmakesmaps.language_points/6/5/16.png](http://a.tiles.mapbox.com/v3/mattmakesmaps.language_points/6/5/16.png). NOTE: You'll have to squint to see the points. They don't show up very well with the black on dark blue background. OR, you can open the tile link above in a new tab.

```Archived Post. Original Image Unavailable.```

The real magic comes when we composite these individual layers together, on-the-fly. From the [docs](http://www.mapbox.com/developers/api/#Map.resources)

> By joining multiple ids with commas into a single :map parameter you can access the MapBox Hosting compositing API to layer multiple tile images, UTFgrids or aggregate the TileJSON of multiple maps into a single map.

That looks something like this [http://a.tiles.mapbox.com/v3/mattmakesmaps.language_lines,mattmakesmaps.language_points/6/5/16.png](http://a.tiles.mapbox.com/v3/mattmakesmaps.language_lines,mattmakesmaps.language_points/6/5/16.png): 

```Archived Post. Original Image Unavailable.```

Pretty cool. You can even flip the z-index in which the layers are composited by simply re-arranging their position in the URL.

That's it for now. The next post will cover how to fork Mapbox's DC-Properties template, allowing us to visualize our maps, accessible via github pages. 
