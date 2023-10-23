---
date: 2011-12-12
title: 'GeoDjango: Standing Up A GeoJSON Web-Service'
---

The models are complete. The database is loaded with some test tabular and spatial data. We're pushing out HTML representations of attribute data using GeoDjango's standard templating functions. Now, the focus moves to visualizing these features' geometries in a spatial context. Just as with a Django QuerySet, GeoDjango provides a GeoQuerySet. <!-- more --> When paired with a spatially-enabled database (e.g. PostGIS, SpatialLite, etc.), the GeoQuerySet provides functionality for querying data using a series of spatial filters, in addition to tabular filters. As a point of reference, the GeoDjango docs have great tables depicting a blow-by-blow comparison of different spatial databases, displaying each available [Spatial Lookup](https://docs.djangoproject.com/en/dev/ref/contrib/gis/db-api/#spatial-lookup-compatibility) and [GeoQuerySet method](https://docs.djangoproject.com/en/dev/ref/contrib/gis/db-api/#geoqueryset-methods). Take note, PostGIS is the clear winner in terms of functionality ;)



## Why GeoJSON?


From the perspective of exporting data, GeoDjango supports a number of formats. The GeoQuerySet methods can represent your model's geometry column in a number of different [formats](https://docs.djangoproject.com/en/dev/ref/contrib/gis/geoquerysets/#geometry-output): GeoHash, GeoJSON, GML, KML, and SVG. Of all these serialization formats, I've found KML to be the most frequently used amongst GeoDjango users. Illustrative of this, three of the four functions in [django.contrib.gis.shortcuts](https://code.djangoproject.com/browser/django/trunk/django/contrib/gis/shortcuts.py) have to do with KML/KMZ. That's awesome, but where is the love for GeoJSON?

KML can be easily consumed by OpenLayers, the king of open source web mapping viewers. But some of the new kids, e.g. leaflet, polymaps, look to favor GeoJSON over KML as an input for dynamically rendered data, not directly consuming KML out-of-the-box. That being said, if you want KML, this [fork of leaflet](https://github.com/shramov/Leaflet/tree/master/src/layer) looks like it will work for you. In my particular project, I'm interested in using leaflet, so GeoJSON was the way to go.

Later on, I'd like to do some speed comparisons, rendering the same featureset using OpenLayers, represented as both KML and GeoJSON, but that's for the future. I'm wondering if OpenLayers will handle the JSON object faster then KML's XML? JSON is just JavaScript after all.



## The Problem


The GeoDjango GeoQuerySet API has built in methods to handle the serialization and de-serialization of a result set's geometries into different formats. The problem is that these methods only wrap the geometries of a result set. For display in a web mapping application, like leaflet, I want to have access to both the geometry in the format of my choosing, as well as the supplementary attributes (name, type, etc.) which provide context for that geometry.

For example, asking for the GeoJSON representation of a given feature through Django's shell, like this:

```python
# Import Models from the Company Application
from company.Models import *
# Create a GeoQuerySet from the primary key, return GeoJSON
qs = Boundary.objects.filter(pk=1).geojson()
# Print GeoJSON representation of geom
print qs[0].geojson
```

Will produce a GeoJSON object like this:

```javascript
{
 "type":"MultiPolygon",
  "coordinates":[
    [
      [
        [
          -122.574295,
          47.856636
        ],
        [
          -122.573924,
          47.85718
        ],
        [
          -122.573719,
          47.85757
        ] // Truncated Verticies
      ]
    ]
  ]
}
```

As shown in the example above, the geometries are returned, but not the tabular attributes associated with that feature. Looking at the [GeoJSON spec](http://geojson.org/geojson-spec.html), there are multiple 'type' values which an object can be constrained by. Using GeoDjango's geoJSON() method will produce a type matching the geometry listed in the associated GeoDjango model (point, line, polygon, etc). The distinction here is that I'd like to return a GeoJSON object of type 'Feature' or 'FeatureCollection'. These types require an additional 'properties' parameter, which can store tabular attributes. From the spec:



> 
A feature object must have a member with the name "properties". The value of the properties member is an object (any JSON object or a JSON null value).




So, the trick now is to dynamically create a GeoJSON object which contains both populated Geom and Properties attributes.



## The fix (vectorformats)


In order to create a fully populated GeoJSON object, we need to bring in some extra assistance. Some quick searching brought me to this stack exchange [response](http://stackoverflow.com/questions/3034482/rendering-spatial-data-of-geoqueryset-in-a-custom-view-on-geodjango), from [Chris Schmidt](http://crschmidt.net/blog/). Chris' vectorformats package handles the serialization and de-serializtion of a variety of formats, including Django Querysets and GeoJSON. From the project [homepage](http://packages.python.org/vectorformats/):



> 
The vectorformats library is designed to make it easy to serialize content from any source to any source within Python. Think of it as a “poor man’s OGR” – a pure Python implementation of transforming features to and from various formats (largely XML based).




Installing vectorformats is as easy as:

``` bash
$sudo easy_install vectorformats
```

From there, as outlined in the above referenced post, it's only a matter of adding a few lines into your GeoDjango app's [view function](https://github.com/mattmakesmaps/geodjango/blob/master/sampling/views.py).

``` python
# Using vectorfeatures module return a GeoJSON FeatureCollection
# for a given boundary ID.
def boundary_detail(request, boundary_id):
    boundary_detail = Boundary.objects.filter(pk=boundary_id)
    djf = Django.Django(geodjango='geom', properties=['name'])
    geoj = GeoJSON.GeoJSON()
    s = geoj.encode(djf.decode(boundary_detail))
    return HttpResponse(s)
```

The resulting GeoJSON object, represented as a 'type' of 'FeatureCollection':

``` javascript
{
  "crs":null,
  "type":"FeatureCollection",
  "features":[
    {
      "geometry":{
        "type":"MultiPolygon",
        "coordinates":[
          [
            [
              [
                -122.574295,
                47.856636
              ],
              [
                -122.573924,
                47.85718
              ],
              [
                -122.573719,
                47.85757
              ] // Truncated Verticies
            ]
          ]
        ]
      },
      "type":"Feature",
      "id":1,
      "properties":{
        "name":"Port Gamble"
      }
    }
  ]
}
```

And there you have it, GeoJSON containing both the geometry and attributes. This output can now be mapped to URL, creating an endpoint such as 'http://my-site.com/geojson/boundary/{boundary_id}/'. Pass this to your web mapping client, and you're ready to rock.
