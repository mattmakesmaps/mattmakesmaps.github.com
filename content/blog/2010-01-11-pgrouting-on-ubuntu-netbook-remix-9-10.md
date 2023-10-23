---
date: 2010-01-11
title: pgRouting On Ubuntu Netbook Remix 9.10
---

While working through Regina Obe and Leo Hsu's [PostGIS In Action](http://www.postgis.us) I thought that I'd jump into the world of routing. My plan was to develop a sample application that could be used to plan bicycle routes throughout the city of Seattle. A quick google search proved that someone has already done it, and done it very well! [http://www.ridethecity.com/](http://www.ridethecity.com/) provides cycling routes using OSM data for many major cities, Seattle included. <!-- more -->

Undeterred and inspired, i decided to compile the [pgRouting](http://pgrouting.postlbs.org/) set of tools for PostGIS and give them a whirl.

My primary tutorial for moving through the install and execution of functions came from the 2009 FOSS4G Tokyo & Osaka workshop entitled, "[FOSS4G routing with pgRouting tools and OpenStreetMap road data.](http://www.google.com/url?sa=t&source=web&ct=res&cd=7&ved=0CCMQFjAG&url=http%3A%2F%2Fwww.osgeo.jp%2Fwordpress%2Fwp-content%2Fuploads%2F2009%2F11%2Fworkshop_manual.pdf&ei=4vdLS63EKIGSsgPFrsGIDA&usg=AFQjCNEoTXqRqtS8fpDXbNLo6H2Nk3cEyg&sig2=RLw7qVqUev7k8pdvzCjXeQ)" Although my installation on Ubuntu Netbook Remix (UNR) 9.10 required a little different setup, this guide definitely got me 99% of the way there.

The majority of my installation woes were caused by the different pathways used on my UNR install of PostgreSQL vs. what are apparently the standard paths.

After attempting to execute cmake to compile pgRouting, I'd be presented with an error stating that the 'POSTGRESQL_INCLUDE_DIR' was not found. A locate command pointed me to the correct path for my PostgreSQL installation. By modifying the FindPostgreSQL.cmake file to search for the correct path, I was back in business.

Following the workshop instructions, I then attempted to create the database directly from the terminal, which yielded the following result.

[sourcecode language="bash"]matt@matt-netbook:~$ createdb -U postgres routing
createdb: could not connect to database postgres: could not connect to server: No such file or directory
 Is the server running locally and accepting
 connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?[/sourcecode]

After reading the documentation associated with "createdb", i tried adding the "-h" flag pointing to "localhost", which solved the problem.

The final error which I ran into had to do with the "$libdir" environment variable. While trying to register the pgRouting functions in my new database, I'd be presented with the following:

[sourcecode language="bash"]psql:/usr/share/postlbs/routing_core.sql:32: ERROR:  could not access file "$libdir/librouting": No such file or directory
psql:/usr/share/postlbs/routing_core.sql:43: ERROR:  could not access file "$libdir/librouting": No such file or directory
psql:/usr/share/postlbs/routing_core.sql:53: ERROR:  could not access file "$libdir/librouting": No such file or directory[/sourcecode]

Getting impatient at this point (i wanted to route!) I modified the SQL files to reference the explicit path of my PostgreSQL lib directory. Once that was done, I had a working routing database!

Loading the sample data, creating the indexes, and executing the queries was amazingly straightforward. To test visualizing the data, I exported one of the tutorial queries directly into a new table.

[sourcecode language="SQL"]SELECT * INTO export
 FROM dijkstra_sp('ways', 10, 20);[/sourcecode]

[caption id="attachment_316" align="alignnone" width="300" caption="The route depicted in red as seen in QGIS."][![qgis_routing](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/qgis_routing-300x175.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/qgis_routing.png)[/caption]

Just for kicks, I tried exporting the data as GeoJSON and visualzing it via OpenLayers.

The following SQL query aggregates the exported line segments into a single GeoJSON object:

[sourcecode language="SQL"]SELECT ST_AsGeoJSON(ST_UNION(the_geom)) AS geom_union
FROM export;[/sourcecode]

Using the [vector-formats](http://openlayers.org/dev/examples/vector-formats.html) OL example, which displays GeoJSON in either EPSG 4326 or 102113, I was able to visualize the line segment with no problem.

[caption id="attachment_317" align="alignnone" width="300" caption="GeoJSON representation of line segment generated using pgRouting, displayed in OpenLayers"][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/openlayers_vector_formats-300x175.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/01/openlayers_vector_formats.png)[/caption]

Well that's all for one day. So it looks like the bike riding app is out, but I'm sure that there will be many more interesting ideas for pgRouting that will come to mind as I continue to explore PostGIS.
