---
title: "Hack GeoDjango Admin with Mapquest Tiles"
date: 2013-05-01
---

The [GeoDjango model](http://geodjango.org/) admin provides a great [OpenLayers](http://openlayers.org/) interface, allowing a user to create geographic features (points, lines, polygons) directly via a web map. Out-of-the-box,
GeoDjango ships with a base GeoAdmin class, using the default OL world borders layer,
as well as a subclass for OSM streets data. The OSM layer is great, and provides a good base
for most use cases. That being said, for [PntTrax](https://github.com/mattmakesmaps/PntTrax), I needed aerial tiles. <!-- more -->My application deals
primarily with the storage of field collected (GPS, field notes, aerial markup, etc.) data.
Our data occur primarily in non-urban areas, where natural features provide a much
better context for orientation then would be expected with any streets layer, OSM or otherwise.
Mapquest provides a good set of aerial tiles, that can be easily integrated into OpenLayers.

It's easier to place a point on this:

```Archived Post. Original Image Unavailable.```

Then this:

```Archived Post. Original Image Unavailable.```

Hacking the admin to display the [Mapquest aerial tiles](http://developer.mapquest.com/web/products/open/map) is a pretty straightforward process.
From the gis contrib package, we'll modify [options.py](https://github.com/django/django/blob/master/django/contrib/gis/admin/options.py) and its [\_\_init\_\_.py](https://github.com/django/django/blob/master/django/contrib/gis/admin/__init__.py), subclassing the OSMGeoAdmin. Within the [templates
folder](https://github.com/django/django/tree/master/django/contrib/gis/templates/gis/admin),
we'll create two new files, a html template file, that actually points to a javascript file
containing a reference to the Mapquest Open Aerial tiles service.

## Creating mapquestGeoAdminSubclass

The [OSMGeoAdmin class](https://github.com/django/django/blob/master/django/contrib/gis/admin/options.py#L132-L139) is a subclass of the base GeoModelAdmin. The GeoModelAdmin contains a set of
configuration parameters for the map, covering basic setup. The OSMGeoAdmin class utilizes
many of those configuration parameters, but configures the map for a spherical mercator projection.
Since our Mapquest Aerial Tiles also require a spherical mercator projection, our class will
be a subclass of the OSMGeoAdmin. 

We add the following code below the OSMGeoAdmin class definition.

``` python
    # Subclass OSMGeoAdmin, pointing to the To-Be-Created
    # Mapquest template file.`
    class mapquestGeoAdmin(OSMGeoAdmin):
        map_template = 'gis/admin/mapquest.html'
```

## Modify the GIS Admin Package's \_\_init\_\_.py

The `__init__.py` file imports the `OSMGeoAdmin` class, let's modify it to include our
new subclass

``` python
    # From this
    from django.contrib.gis.admin.options import OSMGeoAdmin
    # To this
    from django.contrib.gis.admin.options import OSMGeoAdmin, mapquestGeoAdmin
```

## Create mapquest.js

Again, using the OpenStreetMap implementation as a reference, we can see that `osm.js` extends the basic `openlayers.js` file, but replaces the contents of the `base layer`
block with an a reference to the OpenStreetMap layer. 

We'll create a similar file in this package called `mapquest.js`. The contents are as
follows:

{% raw %}
```
{# Source: http://openlayers.org/dev/examples/mapquest.html #}
{% extends "gis/admin/openlayers.js" %}
{% block base_layer %}
        new OpenLayers.Layer.XYZ(
            "Imagery",
            [
                "http://otile1.mqcdn.com/tiles/1.0.0/sat/${z}/${x}/${y}.png",
                "http://otile2.mqcdn.com/tiles/1.0.0/sat/${z}/${x}/${y}.png",
                "http://otile3.mqcdn.com/tiles/1.0.0/sat/${z}/${x}/${y}.png",
                "http://otile4.mqcdn.com/tiles/1.0.0/sat/${z}/${x}/${y}.png"
            ],
            {
                attribution: "Tiles Courtesy of <a href='http://open.mapquest.co.uk/' target='_blank'>MapQuest</a>. Portions Courtesy NASA/JPL-Caltech and U.S. Depart. of Agriculture, Farm Service Agency. <img src='http://developer.mapquest.com/content/osm/mq_logo.png' border='0'>",
                transitionEffect: "resize"
            }
        )
{% endblock %}
```
{% endraw %}

## Create mapquest.html

In the same folder, we'll create an html template file, pointing to our javascript file.
We'll call the file `mapquest.html`. Here are the contents:

{% raw %}
```
{% extends "gis/admin/openlayers.html" %}
{% block openlayers %}{% include "gis/admin/mapquest.js" %}{% endblock %}
```
{% endraw %}

## In admin.py, Reference mapquestGeoAdmin

The last step is to replace references to `GeoModelAdmin` or `OSMGeoAdmin` to
`mapquestGeoAdmin`.

```python
class PntTraxGeoAdmin(admin.mapquestGeoAdmin):
    """Base Class for Geometry Table Admin"""
    list_display = ('name','collectDate','group','featurePurpose','collectionMethod')
    list_editable = ('featurePurpose','group','collectionMethod')
    list_filter = ('featurePurpose','group__name')
```

And there you go. Your Django GeoAdmin interface is now rocking aerial tiles courtesy
of Mapquest.