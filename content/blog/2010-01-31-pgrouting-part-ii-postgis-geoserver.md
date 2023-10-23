---
date: 2010-01-31
title: 'pgRouting Part II: PostGIS + Geoserver'
---

Since compiling [Orkney's pgRouting extension](http://pgrouting.postlbs.org/) to PostgreSQL/PostGIS, I've decided to try my hand at creating a simple web interface to poke into the database. The current setup is as follows: <!-- more -->



	
  * Display: OpenLayers

	
  * Renderer: Geoserver (via non-cached WMS)

	
  * Spatial Backend: PostGIS/pgRouting enabled PostgreSQL

	
  * Data: [Public GIS data](http://www.cob.org/services/maps/gis/index.aspx) from the city of Bellingham, Washington's GIS department.


For the sake of brevity, (but really because both TOPP has created some [fantastic guides](http://workshops.opengeo.org/opengeo-stack/)) I won't go into the specifics of installing all the pieces. Just as an FYI, remember to set your 'JAVA_HOME' environment variable and make sure that you don't have things trying to use the same port!

The Bellingham data is currently stored in [NAD83 State Plane WA North Feet](http://www.spatialreference.org/ref/esri/102748/), a typical projection for this area. This projection however, is not part of the EPSG projection set, and as such is not included in a vanilla install of PostGIS.

In order to add this to the collection of spatial reference systems used by my PostGIS install, I went with the ridiculously cool [spatialreference.org](http://spatialreference.org) site (A [crschmidt](http://crschmidt.net/), [dbsgeo](http://dbsgeo.com/), [hobu](http://hobu.biz/), and [umbrella](http://umbrellaconsulting.com/) joint, hah). Navigating to the projection's page gives me the option to generate an [INSERT](http://www.spatialreference.org/ref/esri/102748/postgis/) statement, adding the projection's info into my database.

To load shapefiles into the PostGIS database, I chose to use the SPIT plugin for QGIS. Loading the data was fairly straightforward. I had an issue with a datefield that was present in the source shapefile, and had to delete the column manually using Open Office Database. I haven't found a way to delete fields from a shapefile using QGIS.

[caption id="attachment_327" align="alignnone" width="300" caption="The SPIT Interface"][![spit](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/spit-300x175.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/spit.png)[/caption]

After uploading the streets data into my PostGIS database, the next step was to transform the geometry into the Web Mercator 900913 Projection. This was done using standard PostGIS functions, adding a new, second, geometry column to the existing streets table. This reprojected data was then exported from my staging PostGIS database as a shapefile using the QGIS, 'Save As Shapefile' tool, and re-imported into my production database (with the routing functions).

With data stored in the web mercator projection, inside of our PostGIS/pgRouting database, the next step was to add the layers to Geoserver. Using Geoserver 2.x, the process included the following steps (all done through the web-admin).



	
  * Add the new data store pointing the PostGIS database.

	
  * Add new layers (resources) which point to the tables of interest in our PostGIS database.


After creating the connections between PostGIS and Geoserver, the creation of WMS services is taken care of, allowing us to roll them into OpenLayers with relative ease.

I guess this got a little off-topic from what I originally wanted to write about. I think that I'll save the actual breakdown of my OL code (taking a user's map click to and using it to calculate a route to the nearest fire-station as determined by manhattan distance, as opposed to euclidean distance) for another day.
