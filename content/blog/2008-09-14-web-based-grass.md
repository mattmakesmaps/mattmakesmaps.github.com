---
date: 2008-09-14
title: Web-Based GRASS!
---

Whoo-hoo! After a number of tweaks, I finally was able to install command-line GRASS GIS onto this site. I've been working off of Aaron Racicot's [tutorial](http://www.reprojected.com/geoblog/how-tos/gis-on-a-shared-hosting-environment-the-magic-of-not-having-root/) for about a week now. Actually being able to understand and fix some of the errors in the ./configure process is feeling pretty rewarding. I had to manually set the pathways for FFTW and TK using --with-fftw-includes and --with-tclktk-includes respectively. Also, ran into <!-- more --> an issue with NAD2BIN not being explicitly laid out as well, but I was lucky enough to find a forum post stating that I needed to use the command, 'export NAD2BIN=/home/.../bin/nad2bin'.

I'm not all that familiar with the command line syntax of GRASS, so I actually had to use a local install to view some sample syntax, and modify it for use on the web instance. Pretty fun.

I'm wondering if there is anyway for a user to access the geoprocessing functionality of this GRASS install w/o logging into the server. Furthermore, could it be possible to tie this into a mapserver / openlayers install? It would be amazing to recreate this [ESRI on-the-fly geoprocessing example](http://resources.esri.com/help/9.3/arcgisserver/apis/javascript/arcgis/help/jssamples_start.htm#jssamples/GPviewShed.html) using only open source GIS tools.

Anyways, it's a pretty cool feeling to be able to upload a file to the server, run a geoprocessing task on it, and download the results back to my machine!


> GRASS 6.2.3 (spearfish):~/usr/local/bin > r.shaded.relief map=elevation.dem shadedmap=shade_test altitude=30 azimuth=270 zmult=2 scale=1
Calculating shading, please stand by.
100%
Color table for [shade_test] set to grey

Shaded relief map created and named [shade_test].
GRASS 6.2.3 (spearfish):~/usr/local/bin > r.out.tiff input=shade_test output=/home/matthewkenny/usr/local/gis_workspace/shade_out compression=none
WARNING: The map <shade_test> in mapset <user1> is a floating point map.
Decimal values will be rounded to integer!
GRASS 6.2.3 (spearfish):~/usr/local/bin >


And the output image downloaded back to my computer:

[![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/09/shade_out-300x225.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/09/shade_out.jpg)

[![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/09/shade_out.tif)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/09/shade_out.tif)
