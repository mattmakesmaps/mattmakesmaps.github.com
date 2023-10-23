---
date: 2008-09-14
title: Python GP Strangeness
---

I had to explicitly set the workspace, list the ASCII files, THEN link the two into a single variable for use in the ASCIIToRaster tool. I could have sworn that all I needed to do was to pass along the ASCII variable right into the ASCIIToRaster tool, and it would automatically carry the file path along with it. Maybe that is only the case when you are working with fields, rather than rasters / feature classes?

<!-- more -->

```python
#Batch Convert ASCII to ESRI GRID
#Matthew Kenny
#September 14, 2008
#############################################################
# Import the arcgisscripting module
import sys, arcgisscripting

# Create the Geoprocessor object
gp = arcgisscripting.create()

# Set the workspace
gp.workspace = ("F:/!South_Kona/DEM_XYZ_Tiled/Area1/Group5/ASCII/")

# get list of all the ASCII files in the workspace
asciiList = gp.ListRasters("*DEM*", "*")
ascii = asciiList.next()
print "Starting with " + ascii

# Set initial INPUT file
inputAscii = gp.workspace + "/" + ascii
print inputAscii

# Set initial OUTPUT file
raster = gp.workspace + "/GRID/" + ascii[:-8] + ".img"
print raster

# loop through list of feature classes
while ascii:
#Execute ASCII to Raster
    gp.ASCIIToRaster_conversion(inputAscii, raster, "INTEGER")
    print "File " + raster+ " has been created."
    ascii = asciiList.next()
    print "Moving onto " + ascii
    inputAscii = gp.workspace + "/" + ascii
    print "new inputAscii: " + inputAscii
    raster = gp.workspace + "/GRID/" + ascii[:-8] + ".img"
    print "new output Raster: " + raster
ascii = asciiList.reset()

#remove geoprocessor object from memory
del gp
```
