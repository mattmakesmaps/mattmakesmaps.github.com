---
title: "Creating Label Points With OpenStreetMap Data"
date: 2013-11-13
---
## tl;dr

```sql
-- Use a CTE to create a table of water polygons, aggregating
-- overlapping polygons sharing the same name
WITH polys AS (
    SELECT name, (ST_DUMP(ST_BUFFER(ST_Collect(geometry), 0))).geom AS single_geom
    FROM osm_new_waterareas
    WHERE name IS NOT NULL
    GROUP BY name
)
-- Generate a name and label point for each feature
-- Generate an accurate area attribute by casting to geography
SELECT name, ST_PointOnSurface(single_geom),
       ST_Area(Geography(ST_Transform(single_geom, 4326))) AS area
FROM polys;
```
<!-- more -->

## Overview

[OpenStreetMap](http://www.openstreetmap.org/) is one the most amazing data projects I know of.
The breadth of high-quality, high-precision features contained with the OSM Planet never ceases
to impress me. That being said, the freedom of the OSM data model, which allows this high-level of
flexibility and openness, is not without its problems. Having participated in the OSM edit-a-thon,
I was inspired to deep dive into the OSM tagging ontology, and subsequent rendering workflows. As part 
of this exploration, one of the major problems I've run into was the generation of label points 
derived from Open Street Map data.

The following example is illustrative of the primary problem faced when generating label points,
overlapping geometries. The SQL statement below returns all features within an [imposm](http://imposm.org)
database's `osm_new_waterareas` table named, 'Lake Chelan'. Three geometries are returned, one
representing the entire lake, and two others representing northern and southern sections of the lake.
All three share a name of, `Lake Chelan`, but two are of type `water`, and one is of type `reservoir`.

``` sql
SELECT name, type, osm_id
FROM osm_new_waterareas
WHERE name = 'Lake Chelan';

    name     |   type    |  osm_id   
-------------+-----------+-----------
 Lake Chelan | reservoir |    446718
 Lake Chelan | water     | 135297050
 Lake Chelan | water     |  52045429
```

Click the map below to view each of the three geometries.

```gist 03d7f78a1591971ed2d8 chelan.geojson```

Rendering label points using PostGIS's [ST_PointOnSurface()](http://postgis.org/docs/ST_PointOnSurface.html) 
function would yield three separate points, as illustrated below. I however, would like only a single
label point for each feature.

```gist 9cf8b49844cb43dfae81 chelan.geojson```

## Step By Step

In addition to a single label point, I'd also like to attribute that label point with an area value,
to be used as a toggle for display at varying zoom levels.

The query at the beginning of the post generates this output table. Let's walk through the major parts
that make up the `single_geom` parameter, step-by-step.

### Step One: ST_Collect()

The [ST_Collect()](http://postgis.net/docs/manual-1.4/ST_Collect.html) in PostGIS is an aggregate
function. Just as a `GROUP BY` clause can be used to aggregate features sharing a common attribute
value, `ST_Collect()` can be used to group geometries. In this case, we'll use it to group
geometries that are associated with the same name. See the code snippet below.

``` sql
-- Execute ST_Collect() Function, creating a MULTIPOLYGON
-- for all features sharing a common name.
WITH collected as (
    SELECT name, ST_Collect(geometry) AS geometry
    FROM osm_new_waterareas
    WHERE name = 'Lake Chelan'
    GROUP BY name
) SELECT name, ST_GeometryType(geometry), ST_NRings(geometry)
FROM collected;

-- Results
    name     | st_geometrytype | st_nrings 
-------------+-----------------+-----------
 Lake Chelan | ST_MultiPolygon |         4
```

This produces the following MULTIPOLYGON result.

```gist 8d4cdbc307deb6d7977f```

### Step Two: ST_Buffer()

The [ST_Buffer()](http://www.postgis.org/docs/ST_Buffer.html) function is used as somewhat of a hack.
It has the special quality of creating from its inputs, a new OGC-compliant geometry. This means that
when given a set of input geometries, and a '0' buffer-distance value, we are essentially left with a
geometry clean geometry that represents the outer-most ring of our MULTIPOLYGON created during the
`ST_Collect()` operation.

```sql
-- Add a 0-distance buffer to our above result, creating a
-- new single polygon representing the outer ring.
WITH coll_buff as (
    SELECT name, ST_Buffer(ST_Collect(geometry), 0) AS geometry
    FROM osm_new_waterareas
    WHERE name = 'Lake Chelan'
    GROUP BY name
) SELECT name, ST_GeometryType(geometry), ST_NRings(geometry)
FROM coll_buff;

-- Results
    name     | st_geometrytype | st_nrings 
-------------+-----------------+-----------
 Lake Chelan | ST_Polygon      |         1
```

Now we're really looking good. We've got a single polygon that represents our water feature.

```gist b76fd3997f99086f6d24```

I could run `ST_PointOnSurface()` against the geometry above, and return a single label point. So what
more needs to be done? The example above works because all records within my OSM planet file with water
features named `Lake Chelan` happen to occur in the same spot. It's a very unique name. What happens
when I run the same query against a more common name, `Twin Lakes`?

```sql
-- Re-execute with a much more common name
WITH coll_buff as (
    SELECT name, ST_Buffer(ST_Collect(geometry), 0) AS geometry
    FROM osm_new_waterareas
    WHERE name = 'Twin Lakes'
    GROUP BY name
) SELECT name, ST_GeometryType(geometry), ST_NRings(geometry)
FROM coll_buff;

-- Our results generate a MULTIPOLYGON, not a POLYGON
    name    | st_geometrytype | st_nrings 
------------+-----------------+-----------
 Twin Lakes | ST_MultiPolygon |       272
```

A single record is created, however it is the aggregate MULTIPOLYGON of all features in the globe
that happen to be named `Twin Lakes`. Executing `ST_PointOnSurface()` would return a single point
geometry, not the 272 unique label points we need.

### Step Three: ST_Dump()

Now that our `ST_Collect()` + `ST_Buffer()` combo has given us a mixed bag of POLYGON and
MULTIPOLYGON geometries, it's explode these guys into separate features. For that, we'll be using
the [ST_Dump()](http://postgis.refractions.net/docs/ST_Dump.html) function. In the example of our
Lake Chelan polygon, `ST_Dump()` will create for us our same polygon. However, for our example of
the `Twin Lakes` MULTIPOLYGON with 272 rings, we'll get back 272 unique records. This will allow us
to create a label point for each instance of `Twin Lakes`

ST_Dump is a bit tricky in that it doesn't simply return the exploded geom (MULTIPOLYGON --> POLYGON),
but rather a special [geometry_dump](http://postgis.net/docs/geometry_dump.html) data type. From that
resulting datatype, we'll select out the `geom` field, giving us our POLYGON.

```sql
-- ST_Dump - Lake Chelan Example
-- NOTE: Wrapping ST_Dump() call in parentheses, and returning the geom
-- field from the resulting geometry_dump.
WITH coll_buff as (
    SELECT name, (ST_Dump(ST_Buffer(ST_Collect(geometry), 0))).geom AS geometry
    FROM osm_new_waterareas
    WHERE name = 'Lake Chelan'
    GROUP BY name
) SELECT name, ST_GeometryType(geometry), ST_NRings(geometry)
FROM coll_buff;

-- Returns a single feature as expected.
    name     | st_geometrytype | st_nrings 
-------------+-----------------+-----------
 Lake Chelan | ST_Polygon      |         1


-- ST_Dump - Twin Lakes Example
WITH coll_buff as (
    SELECT name, (ST_Dump(ST_Buffer(ST_Collect(geometry), 0))).geom AS geometry
    FROM osm_new_waterareas
    WHERE name = 'Twin Lakes'
    GROUP BY name
) SELECT name, ST_GeometryType(geometry), ST_NRings(geometry)
FROM coll_buff;

-- Returns each of the 272 rings of the MULTIPOLYGON as
-- unique polygon features.
    name    | st_geometrytype | st_nrings 
------------+-----------------+-----------
 Twin Lakes | ST_Polygon      |         1
 Twin Lakes | ST_Polygon      |         1
 Twin Lakes | ST_Polygon      |         1
 Twin Lakes | ST_Polygon      |         1
-- NOTE: Selection of results shown.
```

### Step Four: Generate An Area Attribute

In order to have some type of filtration attribute to filter the display of our labels
at different zoom levels, we'll be calculating an area attribute. Since our data are in
the Spherical Mercator projection common to all web maps, we have inherit problems 
generating accurate area calculations directly. One solution to this is to CAST our 
geometries into a PostGIS [geography](http://workshops.boundlessgeo.com/postgis-intro/geography.html)
data type.

As a prerequisite to the Geometry --> Geography cast, we need to first use
[ST_Transform](http://www.postgis.org/docs/ST_Transform.html) to convert our data from the
Spherical Mercator projection to WGS84 Lat/Lng.

The area calculation snippet looks like this
```sql
ST_Area(Geography(ST_Transform(single_geom, 4326))) AS area
```

And wrapped into our larger label point SQL statement, looks like this.

```sql
WITH polys AS (
    SELECT name, (ST_DUMP(ST_BUFFER(ST_Collect(geometry), 0))).geom AS single_geom
    FROM osm_new_waterareas
    WHERE name = 'Lake Chelan' 
    GROUP BY name
)
-- Generate a name and label point for each feature
-- Generate an accurate area attribute by casting to geography
SELECT name, ST_PointOnSurface(single_geom),
       ST_Area(Geography(ST_Transform(single_geom, 4326))) AS area
FROM polys;
```

Just to drive home the importance of accurate area calculations, our call using the CAST
to geography returns a value of 132.028 square kilometers, while using the raw Spherical
Mercator geometry returns an area value of 295.106 square kilometers! Our geography-based
area calculation is much more in line with the 135 square kilometer value
[reported by wikipedia](http://en.wikipedia.org/wiki/Lake_Chelan).

## Wrap-up

And finally, after all that effort, we get the point shown below.

```gist cb1f750979bbcf40a8e5```

### Speeding Up With Indexes

While this exercise is extremely fast on our small test dataset, scaling this up to an
entire imposm OSM planet import is another thing entirely. To facilitate this process,
both spatial and non-spatial indexes can be utilized. 

I created a partial btree index on the name column, a functional index on the transformation
of geometric data to EPSG 4326, and a gist index already existed on the geometry column
as part of the imposm import.

```sql
-- Create a partial index on names that are not null.
CREATE INDEX idx_waterareas_name_partial ON osm_new_waterareas (name) WHERE name IS NOT NULL;
-- Create a functional index on the transformation
CREATE INDEX idx_waterareas_4326 ON osm_new_waterareas USING gist(ST_Transform(geometry, 4326));
```

This process, after applying indexes, took about 5.4 minutes to create. That is, to create a global
set of label points for ~185,000 named water features within the OSM planet file, with both a name and 
dynamically calculated area attribute. This test was performed on modest desktop hardware, on a PostGIS 
instance that was already hot (had been running, been used for querying water area data as part 
of this experiment).