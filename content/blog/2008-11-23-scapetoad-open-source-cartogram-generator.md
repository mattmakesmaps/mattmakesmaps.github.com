---
date: 2008-11-23
title: ScapeToad - Open Source Cartogram Generator
---

[ScapeToad](http://chorogram.choros.ch/scapetoad/index.php) provides an amazingly easy to use interface for creating cartograms. Accepting shapefiles directly as input, it has a wide variety of fine-tunable settings in addition to its presets. The ability to utilize multiple layers as weights also seems like a pretty exciting feature. Written entirely in java, it's platform-independent, another plus for mac and / or linux GIS users. <!-- more -->

[caption id="attachment_127" align="alignnone" width="300" caption="Above: The counties of Washington State."][![WA_Green](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/wa-300x202.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/wa.jpeg)[/caption]

What is so cool about this program is that not only does it use shapefiles as an input, but it generates shapefiles as output as well. A SizeError attribute is also generated to allow a user to easily control map symbology using GIS software. In addition to this, one can also export as an SVG directly to use in a graphics editor.

The cartogram below was generated using the percentage of a county's total area covered by state routes. The lighter colors indicate a smaller distortion in size while the darker colors indicate a larger distortion in size. Note the I-5 corridor in Western Washington is heavily affected using this criteria.

[caption id="attachment_128" align="alignnone" width="300" caption="Above: The result. "][![WA_Cartogram](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/cartogram2-300x212.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2008/11/cartogram2.jpeg)[/caption]

ScapeToad - [http://chorogram.choros.ch/scapetoad/index.php](http://chorogram.choros.ch/scapetoad/index.php)

Data (WA DOT) - [http://www.wsdot.wa.gov/](http://www.wsdot.wa.gov/)
