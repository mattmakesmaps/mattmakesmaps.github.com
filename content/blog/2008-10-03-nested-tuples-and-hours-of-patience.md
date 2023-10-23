---
date: 2008-10-03
title: Nested Tuples and Hours of Patience
---

I've finally got a working python script that does the following:

-Reads each of the vertexes of a polyline shapefile using 'OGR'

-Runs the vertexes through the Douglas-Peucker smoothing algorithm.

-Generates an output text file using 'stdout' with the resulting 'smoothed' coordinates (in whatever linear unit of measurement that the source shapefile happens to be in).

<!-- more -->
This has taken quite awhile to get this far, but it has been an excellent learning experience. The breakthrough for tonight was understanding that the Douglas-Peucker algorithm which I downloaded from mappinghacks required a nested tuple as an input. Basically then, I needed to figure out how translate the extracted vertexes from the source shapefile into the tuple object.

Things started to go bad, really bad, when I attempted to create formatted output files for the extracted vertexes... and re-import them as input files for the DP algorithm. It's all good now though, and the code looks (a bit) cleaner.

The next step will be to take this output text file and build another shapefile from it...

Here is a link to the heavily commented [script](http://www.mkgeomatics.com/scripts/dp_100308.py) (right-click, select 'save-as') as it stands thus-far.
