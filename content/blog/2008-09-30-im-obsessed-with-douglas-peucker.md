---
date: 2008-09-30
title: I'm Obsessed With Douglas-Peucker
---

The Problem: Web map vector overlays take a really long time to load if there are too many verticies.

The Idea: Use the Douglas-Peucker algorithm to reduce the number of verticies thereby decreasing load time.

The [Proposed] Solution: Using a version of the Douglas-Peucker algorithm written in Python, input a source shapefile, and output a smoothed shapefile through the following steps. This script currently uses board coordinates, and as such, is not 'spatially' enabled. The trick is to make it except shapefiles as an input source.
<!-- more -->

-Extract each of the verticies using ogrinfo, pipe to grep, and export to output csv file.
$ogrinfo -al input_polyline.shp | grep linestring | tr -d "LINESTRING()" > output.csv

-Reformat the verticies [somehow], so that they can be readable to the python script.

-Rebuild shapefile from output csv with projection information using GDAL/OGR.

So my plan is to essentially to break down the shapefile, smooth it, and build it back up again, using open source tools. We'll see how far this goes.

Some reading:

[Douglas-Peucker Algorithm](http://en.wikipedia.org/wiki/Ramer-Douglas-Peucker_algorithm)

[Existing GRASS Implementation](http://users.ox.ac.uk/~orie1848/tutorial.html)

[Existing ArcGIS Implementation](http://webhelp.esri.com/arcgisdesktop/9.2/index.cfm?id=530&pid=513&topicname=Simplifying_and_smoothing_features)

-
