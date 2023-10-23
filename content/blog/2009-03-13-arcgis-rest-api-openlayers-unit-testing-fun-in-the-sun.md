---
date: 2009-03-13
title: ArcGIS REST API / OpenLayers / Unit Testing = Fun In The Sun
---

Until today, I had never truly appreciated the value of unit testing. I recently had the need to bring [these ArcGIS REST controls](http://dev.openlayers.org/sandbox/august/openlayers/openlayers-2.6/examples/ags/index.html), designed for version 2.6 of OpenLayers, into the current development version. Having no real idea how to get started on this process, I looked to the unit tests as a guide <!-- more --> to what needed to be changed. One might be asking why this was necessary, when the team over at Avencia just put together a great [ArcGIS REST Plugin](http://trac.openlayers.org/ticket/1749) that has made its way into the trunk for the upcoming 2.8 release. The answer is that both plugins do different things well. The older AGSControls can perform 'Identify' and 'Geoprocessing' operations rather well, while the Avencia plugin does a great job at displaying and querying a subset of a layer resource.

In any event, the [Test.AnotherWay](http://straytree.com/TestAnotherWay/doc/index.html) suite, used by OpenLayers, provides an easy-to-navigate interface for debugging javascript code.

In two steps I was able to begin the debugging process.

First, adding the unit test for the AGSControl to the 'list-tests.html' file located in the 'tests' folder of a development version of OpenLayers. This unit test, written by the developer, needs to manually downloaded and incorporated into the standard series of tests that come with OpenLayers. As we can see from the image below, this particular test was written as an html file and placed into the 'Control' sub-directory of the 'tests' directory.

[caption id="attachment_256" align="alignnone" width="224" caption="Above: Adding a link to the unit test for use by Test.AnotherWay"][![Adding a link to the unit test for use by Test.AnotherWay](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/list_test.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/list_test.png)[/caption]

After adding the test, we open up 'run-tests.html' in the browser and select 'AgsControl' from the list. After the test has executed, the results are provided to us. With the red light of failure burning bright, we might think to abandon all hope. We are, however, given the cause and location of the failure, an invaluable clue as to where to start debugging. Time to soldier on.

[caption id="attachment_257" align="alignnone" width="300" caption="Above: Unit Test Failure"][![Above: Unit Test Failure](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/ags_fail-300x98.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/ags_fail.png)[/caption]

Using these test results as a road map, even I can eventually debug a plugin. The green light of success offers a reassuring reminder that all is well in the GIS world.

[caption id="attachment_259" align="alignnone" width="300" caption="Above: Successful Unit Test"][![Above: Successful Unit Test](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/ags_success1-300x112.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/03/ags_success1.png)[/caption]

I've taken away a few things from this experience. Firstly, I'm again deeply impressed by the time and effort that developers in the Open Source community are putting into these projects. The only reason that I could even dream of modifying any of this source code is due to the fact that the developer of the AGSControls provided such detailed unit tests. These allowed me to wrap my brain around what was going on with the code, and how it could be updated. Taking the time to not only write code, but to also provide tools so that others can understand it and modify it with ease is something that I think I'll always be impressed with. And of course, I'll be continuing to rely on unit tests as debugging tools as i continue my exploration of javascript programming.

In the words of [Dave Bouwman](http://www.davebouwman.net/), who has a whopping fourteen posts in his tag cloud on the subject:


> Unit testing is quite possibly the single best practice for ensuring that your code is bug free (or very nearly bug free!).


His 'fundamentals' article provides a great introduction on the subject: which can be read [here](http://www.davebouwman.net/fundamentals/unittesting.aspx).
