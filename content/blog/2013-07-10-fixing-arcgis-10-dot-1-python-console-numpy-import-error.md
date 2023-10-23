---
title: "Fixing ArcGIS 10.1 Numpy Import Error From Python Console"
date: 2013-07-10
---

At my current employer, we have four Windows 7 64-bit workstations, all running ArcGIS 10.1 under
various license levels. Hilariously, due to some quirk that I haven't been able to figure out,
all machines have been reporting the following traceback when running `>>> import numpy` from
ArcGIS Desktop's internal Python console.
<!-- more -->

```
>>> import numpy
Runtime error 
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "c:\program files (x86)\arcgis\desktop10.1\arcpy\arcpy\__init__.py", line 24, in <module>
    from arcpy.toolbox import *
  File "c:\program files (x86)\arcgis\desktop10.1\arcpy\arcpy\toolbox.py", line 342, in <module>
    from management import Graph, GraphTemplate
  File "c:\program files (x86)\arcgis\desktop10.1\arcpy\arcpy\management.py", line 22, in <module>
    import _management
  File "c:\program files (x86)\arcgis\desktop10.1\arcpy\arcpy\_management.py", line 14, in <module>
    import _graph
  File "c:\program files (x86)\arcgis\desktop10.1\arcpy\arcpy\_graph.py", line 27, in <module>
    import numpy
ImportError: No module named numpy
```

One solution to this is to explicitly append the `PYTHONPATH` environment variable to reference the ArcGIS10.1 Python install's `site-packages` directory.

# Creation of PYTHONPATH Environment Variable

1. From the start menu, right click the `computer` button and select `properties`.

2. Click `Advanced System Settings`.

3. Click the `Advanced` tab, and select the `Environment Variables` button.

4. Click `New` to create a new environment variable.

5. Name the environment variable `PYTHONPATH` and set it's value to point to the `site-packages` directory for ArcGIS. For my instance, this is `C:\Python27\ArcGIS10.1\Lib\site-packages`.

6. Restart the machine. You should now be able to successfully import numpy, arcpy, and any other Python modules installed as part of the ArcGIS 10.x version of Python.
