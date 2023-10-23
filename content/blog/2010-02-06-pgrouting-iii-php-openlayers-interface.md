---
date: 2010-02-06
title: 'pgRouting III: PHP + OpenLayers Interface'
---

With the routing [database configured and populated](http://www.mkgeomatics.com/wordpress/?p=312), and with [geoserver rendering the WMS](http://www.mkgeomatics.com/wordpress/?p=322), now the focus can shift on designing the actual display and functionality.

The conceptual plan is as follows: <!-- more -->



	
  * Extract the geometry of a user's click on the map.

	
  * Pass the extracted geometry to a PHP script, via an HTTP GET request.

	
  * Use the PHP script to pass the geometry as part of an SQL query against the PostGIS/pgRouting database.

	
  * Return the geometry from the database as [GeoJSON](http://geojson.org/), and deserialize it into an OpenLayers vector layer feature.


The code to extract a user's clicked coordinates was taken from [this](http://openlayers.org/dev/examples/click.html) OpenLayers example. It was then modified to pass the xy coordinates to a second function, designed to create a URL which will execute a PHP script.

[sourcecode language="javascript"]trigger: function(e) {
 var xy = map.getLonLatFromViewPortPx(e.xy);
 executeSQL(xy);
 }[/sourcecode]

Passing the XY variable to the executeSQL() function, we are able to now seperate out the individual X and Y coordinates, and apply them to their respective parameters in our URL string.

[sourcecode language="javascript"]// Build the URL
 var json_url = "http://localhost/near_vertex_astar.php?";
 json_url += "x=" + escape(xy.lon);
 json_url += "&y=" + escape(xy.lat);[/sourcecode]

Having constructed the URL, we are now ready to use it to populate an OpenLayers vector layer with data.

[sourcecode language="javascript"]// Make a fresh vector layer, pulling features from our script URL
 json_layer = new OpenLayers.Layer.Vector("GeoJSON", {
 styleMap: myStyles,
 strategies: [new OpenLayers.Strategy.Fixed()],
 protocol: new OpenLayers.Protocol.HTTP({
 url: json_url,
 format: new OpenLayers.Format.GeoJSON()
 })
 });[/sourcecode]

Alright! So where are we at right now? A user has clicked the map, and that click's geometry has been extracted and sent to a PHP script on the server for further work. The PHP script will execute SQL in the PostGIS/pgRouting data base to do the following:



	
  * Find the closest vertex in our routing network to the user's map click. This will be used as a source vertex.

	
  * Find all firestations within 5km of the vertex (which have been pre-attributed with the closest vertex on the routing network to their location).

	
  * Calculate the cost (as defined by total length of the route) from the source vertex to each fire station (really the routing network vertex).

	
  * Return back as GeoJSON only the geometry for the route with the lowest cost.


Why all the hassle with determining the cost? Can't you just use PostGIS' ST_DWithin() function to find the closet firestation to our user's click and create the route? Well you could, but it might not always be the shortest route.

[caption id="attachment_339" align="alignnone" width="300" caption="Euclidean distance versus Manhattan. Which one is shorter?"][![Euclidean distance versus Manhattan. Which one is shorter?](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/02/distance-300x279.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/02/distance.png)[/caption]

This behavior can be respresented in the routing network with the example below. Two different routes are generated from the same source vertex based on the combination of routing algorithm and account for route cost. On the left, the [dijkstra algorithm](http://en.wikipedia.org/wiki/Dijkstra%27s_algorithm) is used to return the route to the closest fire station as the result of an ST_DWithin() query. On the right, the A-Star algorithm is used, and the route costs of all fire stations within a buffer are taken into account. As we can see, a different route and a different station are returned.

[![Comparing the two search algorithms and cost relationships.](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/02/dj_left_astar_right1-1024x338.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/02/dj_left_astar_right1.png)

A link to the JS and PHP scripts can be found at the end of this post. This definitely is not the most elegant solution to working with routing, but in terms of an experiment it was a great learning exercise. I'm really excited to dive deeper into PostGIS and pgRouting. The next step in the process will be incorporating OSM data, and adding in addition attributes which affect cost (speed limits, one-way streets, etc).

View the [PHP](http://mkgeomatics.com/apps/syntaxhighlighter/astar_php.html).

View the [OL JS](http://mkgeomatics.com/apps/syntaxhighlighter/astar.html).
