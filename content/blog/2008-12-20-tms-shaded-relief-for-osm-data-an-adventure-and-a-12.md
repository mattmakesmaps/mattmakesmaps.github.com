---
date: 2008-12-20
title: 'TMS: Shaded Relief for OSM Data (An Adventure and A 1/2)'
---

After reading [Michal Migurski's](http://stamen.com/) excellent post, '[making friends with hillshading](http://mike.teczno.com/notes/hillshading.html)' I decided to try my own hand at producing a TMS-compatible hillshade layer for OSM. Motivated by both the high resolution LiDAR data which I happen to have access to, and the lack of access to ArcDesktop during the winter intersession... I set out on the FOSSGIS path. <!-- more -->

[caption id="attachment_205" align="alignnone" width="259" caption="The resulting overlay"][![The resulting overlay](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/picture-1-259x300.png)](http://www.mkgeomatics.com/apps/openlayers/tiles/ol_working.html)[/caption]

The final output was created in a primarily two-step process. The hillshade was created using Matt Perry's [GDAL DEM tools](http://www.perrygeo.net/wordpress/?p=7). This command line addition to the GDAL suite of tools negated having to import the DEM into a new GRASS location, and exporting the resulting hillshade back out as another format.

The majority of the work, the tiling that is, was done using [GDAL2Tiles](http://www.klokan.cz/projects/gdal2tiles/). This Google Summer of Code project operates under a command line utility which will take your slick GDAL-compatible raster dataset as input, and export a set of tiles exactly to your specs. What's more is the capability to automatically generate google maps and OpenLayers viewing pages which can be directly uploaded to a webspace.

Finally, applying a slight transparency to the OSM layer allowed for the shaded relief to appear from behind. Quick-and-Dirty, [but it works](http://mkgeomatics.com/apps/openlayers/tiles/ol_working.html)!

The pain came when trying to compare the local TMS overlay using OSM vs. Google Maps base data. While using Google Maps street data, we clearly see the LiDAR building footprints align with the vector street centerlines. But switching over to OSM data, we see a drastic skew between the two.

[caption id="attachment_206" align="alignnone" width="300" caption="Google Street Data: Properly Aligned Local TMS"][![Google Street Data: Properly Aligned Local TMS](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/picture-21-300x209.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/picture-21.png)[/caption]

[caption id="attachment_209" align="alignnone" width="300" caption="OSM Data: Note Hawaii Belt Road's location compared to its LiDAR footprint."][![OSM Data: Note Hawaii Belt Rd's location compared to its LiDAR footprint.](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/picture-4-300x297.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/picture-4.png)[/caption]

Initially, I assumed that the problem was the result of some projection issues (EPSG: 4326 vs EPSG: 900913), until I realized that all data sets (LiDAR, Google, and OSM) had matching coastlines. Any distortion resulting from the affine transformation between WGS84 and Spherical Mercator would have clearly showed up along the coast as well as the streets.

A quick look at the [Potlatch](http://wiki.openstreetmap.org/wiki/Potlatch) editor revealed the inaccuracy of the TIGER line files used in this section of the map:

[caption id="attachment_210" align="alignnone" width="300" caption="Potlatch: TIGER data is a good, but not great."][![Potlatch: TIGER data is a good, but not great.](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/potlatch-300x273.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/12/potlatch.png)[/caption]

All-in-all I was extremely impressed with the speed and efficency of using the [GDAL DEM Tools](http://www.perrygeo.net/wordpress/?p=7) as well as [GDAL2Tiles](http://www.klokan.cz/projects/gdal2tiles/). Both projects are great representations of what can be done by individuals who choose to build upon existing FOSSGIS projects, and give back to the community.

Additional Note:
A GRASS-based approach to creating a TMS layer is possible, and has been outlined in [this paper](sunbird.jrc.it/pvgis/doc/paper/2008_OSGeo5_TiledMapServices.pdf) appearing in the OSGeo Journal.
