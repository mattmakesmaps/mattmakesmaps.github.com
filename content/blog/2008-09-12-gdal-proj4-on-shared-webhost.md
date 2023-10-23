---
date: 2008-09-12
title: GDAL / PROJ.4 On Shared Webhost
---

For the past couple of days, I've been putting hours into understanding Aaron Racicot's, '[guide to GIS on a shared hosting service](http://www.reprojected.com/geoblog/how-tos/gis-on-a-shared-hosting-environment-the-magic-of-not-having-root/)'. This has been a great learning experience in working w/ SSH and a full mapserver install from the source. My goal is to host a Mapserver WMS directly from this site. I've got everything in place... probably not configured correctly, probably full of errors, probably will never work (w/o a healthy dose of patience).

I do however, know that I am on the right track, as GDAL/OGR and PROJ.4 are up and running.

<!-- more -->


> [gotti]$ ./ogrinfo -al -so /home/matthewkenny/usr/local/shp_samples/county_polygon.shp
INFO: Open of `/home/matthewkenny/usr/local/shp_samples/county_polygon.shp'
using driver `ESRI Shapefile' successful.

Layer name: county_polygon
Geometry: Polygon
Feature Count: 39
Extent: (576751.628593, 81877.320813) - (2551197.698864, 1355595.418907)
Layer SRS WKT:
PROJCS["NAD_1983_HARN_StatePlane_Washington_South_FIPS_4602_Feet",
GEOGCS["GCS_North_American_1983_HARN",
DATUM["NAD83_High_Accuracy_Regional_Network",
SPHEROID["GRS_1980",6378137.0,298.257222101]],
PRIMEM["Greenwich",0.0],
UNIT["Degree",0.0174532925199433]],
PROJECTION["Lambert_Conformal_Conic_2SP"],
PARAMETER["False_Easting",1640416.666666667],
PARAMETER["False_Northing",0.0],
PARAMETER["Central_Meridian",-120.5],
PARAMETER["Standard_Parallel_1",45.83333333333334],
PARAMETER["Standard_Parallel_2",47.33333333333334],
PARAMETER["Latitude_Of_Origin",45.33333333333334],
UNIT["Foot_US",0.3048006096012192]]
COUNTY_COD: Integer (2.0)
COUNTY_FIP: String (3.0)
COUNTY_NM: String (15.0)
ECY_REGION: String (4.0)
AIR_REGION: String (46.0)
[gotti]$
