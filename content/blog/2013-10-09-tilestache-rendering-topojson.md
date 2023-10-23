---
title: "TileStache: Generate [Topo|Geo]JSON Vector Tiles"
date: 2013-10-09
---
Vector tiles are simply tiled representations of geographic data, much like raster tiles
used to power typical web maps such as OpenStreetMap. They differ from traditional 
raster tiles however in that instead being an image, they actually contain the 
geographic coordinates and attributes of the features that exist within their bounding boxes.
Vector tiles can be used for a variety of different rendered outputs within a modern web-mapping
application. One examples can include serving as an intermediate step in the processing of
raster tiles. This approach is adopted by Mapbox, who have defined their own
[mapnik-vector-tile](https://www.mapbox.com/blog/vector-tiles/) spec. This allows for amazing
styling possibilities, allowing raster tiles to be generated with different styles quickly and
efficiently based on the same dataset. Another approach relies on transmitting vector data directly
to the client, where client-side libs render data as SVGs directly in the browser. [D3.js](http://d3js.org)
is a JavaScript library that can do this just this, as illustrated by [Sam Matthews](http://bl.ocks.org/svmatthews/6081504). Now that we know what they're good for, how can we take our own local geographic data, and render our own?
<!-- more -->
[TileStache](http://tilestache.org) is a Tiled Mapping Server written in Python. The core committer on the project
is [Michael Migurski](http://mike.teczno.com), formerly of [Stamen Design](http://stamen.com) and now CTO of
[Code for America](http://www.codeforamerica.org). He's blogged extensively about his development of
the TileStache project to incorporate both use-cases illustrated above
([pipeline to raster tiles via mapnik](http://mike.teczno.com/notes/postgreslessness-mapnik-vectiles.html),
[client-side rendering](http://mike.teczno.com/notes/vector-tile-rendering-numbers.html))

I've been reviewing the source code for this project for the past few weeks and have been impressed at the accessibility
of the code to a novice Python developer such as myself. In any event, I'd like to use this post to drop some
quick notes on how to get things up and running.

## Prerequisites

* TileStache Installed With Dependencies
* PostGIS Installed

## Data

The dataset I imported for rendering is known as 'processed_p', which can be downloaded [here](http://openstreetmapdata.com/data/land-polygons). This is the land dataset used in the OpenStreetMap basemap. I picked this dataset because it is highly detailed, large (365 MB), and projected in spherical mercator. This is a perfect example of a dataset in which you would really want to have a server spraying out just those specific slices of data (tiles) a user has requested, from the larger whole.

## Process

1. Create a PostGIS Database, 'ts_data'

``` bash
$ created ts_data
$ psql -d ts_data
ts_data# CREATE EXTENSION postgis;
CREATE EXTENSION
ts_data=# CREATE SCHEMA osm;
CREATE SCHEMA
```

2. Load processed_p Shapefile into PostGIS

NOTE: See the excellent shp2pgsql/pgsql2shp cheat sheet by [BostonGIS](http://www.bostongis.com/pgsql2shp_shp2pgsql_quickguide_20.bqg)
``` bash
# NOTE YMMV, MAY NEED TO ADD ADDITIONAL PSQL CONNECTION PARAMS. SEE '$psql --help'
$ shp2pgsql -I -s 900913 land_polygons.shp osm.land_polygons_split | psql -d ts_data 
```

3. Create a TileStache Config

TileStache relies on a user-defined configuration file to define a layer, accessible by a client.
In this context, a layer is essentially a combination of a backing dataset (e.g. shapefile, MBTiles,
PostGIS database, etc.) and instructions on how those data should be rendered (e.g. Vector Tiles, UTF-GRID, Mapnik, etc.)

An example configuration file can be found [here](https://github.com/mattmakesmaps/TileStache-Experiment/blob/master/config_files/topojson.cfg#L84-L99). I've highlighted the relevant block, which is excerpted
below. This example uses the VecTiles provider ([docs](http://tilestache.org/doc/TileStache.Goodies.VecTiles.html)),
which requires a PostGIS backing store, and produces tiles in geojson, topojson, or Mapnik Vector Tile (MVT) format.
Comments have been added below for clarity.
 
{% raw %}
``` javascript
"osm-processed_p1": { // Layer Name
    // Sets ACCESS-CONTROL-ALLOW-ORIGIN header to "*"
    "allowed origin": "*", 
    "provider": {
        "class": "TileStache.Goodies.VecTiles:Provider",
        // PostGIS Connection Info
        "kwargs": { 
            "dbinfo": {
                "host": "localhost",
                "user": "matt",
                "database": "ts_data"
            },
            // An array of queries for each zoom level.
            // One query == Used for All Zooms
            "queries": [ 
                "SELECT gid, geom AS __geometry__ FROM osm.land_polygons_split"
            ]
        }
    }
}
```
{% endraw %}

4. Start the Server.

TileStache comes pre-canned with it's own simple development server. If installing TileStache from a python package
manager, It can be accessed from the command line via `$ tilestache-server.py`.

The server should now be accessible via [http://127.0.0.1:8080/](http://127.0.0.1:8080/). "TileStache bellows hello."

5. Request a Tile.

From the [API docs](http://tilestache.org/doc/): "TileStache URLs are based on a Google Maps-like scheme":

```
/{layer name}/{zoom}/{column}/{row}.{extension}
```

Utilizing the layer name referenced in the configuration example above, "osm-processed_p1", a tile request would look like:

```
http://127.0.0.1:8080/osm-processed_p1/9/147/196.topojson #TopoJSON
http://127.0.0.1:8080/osm-processed_p1/9/147/196.json #GeoJSON
http://127.0.0.1:8080/osm-processed_p1/9/147/196.mvt # Mapnik Vector Tile
```

Looking at the HTTP requests above, we can see that we're calling on the same layer, e.g. the same backing datastore,
but rendering those same data into three different output formats. Pretty neat.

## Wrapping Up

TileStache vector tiles can be rendered in any number of client side JS libraries that support GeoJSON or TopoJSON.
Pushing the above requested TopoJSON tile to github, we can view the rendered geometries with their attributes.

```
gist 6896905 s00_9_147_196.topojson
```

TileStache provides an easy-to-use point of entry for vector tiles. In a later post I'll go over some of the profiling
experiments I've been running, using TileStache's dynamic line simplification, and it's affects on file size as well as
the aesthetic affects on representations of these geometries.

I'll also be writing out the work I did extending TileStache to support calls to the [Giphy Labs API](http://labs.giphy.com): [http://mattmakesmaps.github.io/TileStache-GiphyAPI-Demo/](http://mattmakesmaps.github.io/TileStache-GiphyAPI-Demo/)