---
date: 2010-07-09
title: 'cURL''ing to FeatureServer from PostGIS: Easier then I Thought'
---

So I've finished cutting a draft tileset using mapnik, [depicting bus routes in Bellingham, WA](http://mkgeomatics.com/apps/bus/bus.html). Now that the cartography is well in progress, I'd like to add some interactivity to the map. My first attempt at this will be to utilize MetaCarta (Chris Schmidt)'s [FeatureServer](http://featureserver.org/). <!-- more --> FeatureServer allows one to use standard HTTP verbs to GET representations of data, POST new data, or DELETE existing data. While querying data you can also pass additional URL parameters like a bounding box or attribute to select out a smaller subset of returned representations. I'll be POST'ing a bus stop dataset to FeatureServer as GeoJSON. Once the data are stored in FeatureServer, I'll be able to add popups based on a user's click of a bus stop. <!-- more -->

Getting data stored on my local PostGIS install to my remote FeatureServer instance turned out to be a three step process.

**Step One:** Convert local PostGIS bus stops layer to GeoJSON via OGR

I had originally planned on writing a pg/plsql function to try and output a bash script. The script would cURL each feature individually to my FeatureServer instance. This proved to be way more work then I had expected. What was the solution? [OGR](http://www.gdal.org/ogr/), of course. OGR has read/write drivers for both [GeoJSON](http://www.gdal.org/ogr/drv_geojson.html) and [PostGIS](http://www.gdal.org/ogr/drv_pg.html). This allows one to convert an entire dataset to GeoJSON with a single command (see below).

[sourcecode language="bash"]
ogr2ogr -f "GeoJSON" ogrstops.json PG:"host=localhost dbname=routing user=postgres password=*** port=5432" "wtastops(the_geom)"
[/sourcecode]

**Step 2:** Wrap "coordinate" elements in double brackets

When initially trying to cURL the GeoJSON output to FeatureServer, I was receiving an error stating that a bounding box could not be determined for the first geometry in my dataset. After some trial-and-error, I soon realized that the OGR output FeatureCollection was wrapping each point feature's geometry in a single set of brackets. This type of behavior follows the GeoJSON [specification for a FeatureCollection](http://geojson.org/geojson-spec.html#id9), as far as I can tell. However, in order for FeatureServer to consume this dataset, each point feature is required to be wrapped in a second set of brackets. I used gedit to run the find/replace. Below is an example of a GeoJSON feature which FeatureServer can consume. This individual feature is part of a larger FeatureCollection.

[sourcecode language="js"]

{ "type": "Feature",
          "properties": {
             "POINT_ID": "1000",
             "POINT_NAME": "Fielding at 32nd",
             "SHELTER": "Yes", "BENCH": "No" },
          "geometry": {
             "type": "Point",
             "coordinates": [[-122.474490,48.730021]]}
}
[/sourcecode]

**Step 3:** cURL GeoJSON to FeatureServer

The last step is to actually POST the data to FeatureServer. For that, I used cURL.

[sourcecode language="bash"]

curl -d @ogrstops.json http://mkgeomatics.com/cgi-bin/featureserver/featureserver.cgi/scribble/create.json

[/sourcecode]

Now that the features have been uploaded, we can view them via FeatureServer as [GeoRSS](http://mkgeomatics.com/cgi-bin/featureserver/featureserver.cgi/scribble/all.georss), [KML](http://mkgeomatics.com/cgi-bin/featureserver/featureserver.cgi/scribble/all.kml), [JSON](http://mkgeomatics.com/cgi-bin/featureserver/featureserver.cgi/scribble/all.json), [GML](http://mkgeomatics.com/cgi-bin/featureserver/featureserver.cgi/scribble/all.gml). Neat!
