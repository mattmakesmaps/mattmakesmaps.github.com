---
date: 2009-04-05
title: 'SpatialLite: My First Look'
---

With such a small footprint (a single file) [SpatialLite](http://www.gaia-gis.it/spatialite/) appears to a novice like myself to be a fantastic niche storage solution for spatial data. In an environment where installing larger FOSS databases such as MySQL or PostGIS/PostgreSQL can be prohibitive, Spatial Lite appears to provide a great solution. Using the provided GUI interface, <!-- more --> it's extremely easy for a first-time user to create an sqllite dbase, load multiple shapefiles, and create spatial indexes against them. Analogous to a FOSSGIS ESRI Geodatabase, I can see a lot of potential uses for GIS developers who require the indexing and searching power of a database as well as the portability of formats such as an ESRI Shapefile or KML.

It looks like a [QGIS connection](http://geobabble.wordpress.com/2009/03/26/spatiallite-support-in-qgis/) is in the works for the 1.1 release as well.

I'm not sure if it's already being done, but I'd bet that it would be pretty easy to put together a SpatialLite / [FeatureServer](http://featureserver.org/) combination, considering its native support for so many spatial-backends.

Some tutorials I've found to be very helpful have come from: [BostonGIS](http://www.bostongis.com/PrinterFriendly.aspx?content_name=spatialite_tut01) and the [SpatialLite project site](http://www.gaia-gis.it/spatialite/spatialite-2.2_tutorial.html).
