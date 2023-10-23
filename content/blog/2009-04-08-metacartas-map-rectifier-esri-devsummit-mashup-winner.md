---
date: 2009-04-08
title: MetaCarta's Map Rectifier + ESRI DevSummit Mashup Winner :)
---

I never knew about the [MetaCarta Labs' Map Rectifier](http://labs.metacarta.com/rectifier/) tool, but I'll expect to be using it more in the future. After uploading an image to the site, a user has full control over the creation of Ground Control Points. The advanced nature of this tool is shown though included RMS error reporting as well as the choice between multiple transformation algorithms. <!-- more --> In addition to uploading your own content, a user has the ability to add GCPs for other users' uploads as well.

```Archived Post. Image Removed.```

What's really amazing is the ability to directly access rectified images via WMS overlay. Each image hosted on the site is given a unique URL, we can insert into our favorite web mapping clients.

To try it out, I used the [ExtMap - Mashup Framework](http://resources.esri.com/arcgisserver/apis/javascript/gmaps/index.cfm?fa=codeGalleryDetails&scriptID=16067) developed by ArcGIS user [alperdincer](http://resources.esri.com/arcgisserver/apis/javascript/gmaps/index.cfm?fa=codeGallery&authorID=alperdincer). This particular application framework was one of the winners at 2009 ESRI DevSummit, with good reason. I was able to quickly pass in the MetaCarta Labs URL, allowing the ExtMap application to consume and display the WMS layer with ease.

```Archived Post. Image Removed.```

In addition to WMS layers, we can add in KML/GeoRSS as well as ArcGIS Server Dynamic/Tiled Map Layers.

```Archived Post. Image Removed.```

Easy as pie? Piece of Cake? Yes. It's innovative projects like these that keep pushing me to learn more about web mapping technology. Big thanks go out to crschmidt (who i assumed was involved w/ the project) at MetaCarta and [alperdincer](http://resources.esri.com/arcgisserver/apis/javascript/gmaps/index.cfm?fa=codeGallery&authorID=alperdincer) for putting together two great products.

On a final note, the MetaCarta Rectifier has the ability to export out images as geotiffs, allowing us to consume them in our desktop GIS applications. A quick check in ArcCatalog of the Chernobyl sample image I exported out revealed a WGS84 GCS. I can see some really nice workflows combining this tool with tiling programs such as [GDAL2Tiles](http://www.klokan.cz/projects/gdal2tiles/) for painless TMS creation.
