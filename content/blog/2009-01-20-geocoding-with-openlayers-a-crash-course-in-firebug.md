---
date: 2009-01-20
title: 'Geocoding with OpenLayers: A Crash Course In Firebug'
---

The vacation is over. A new job and a new semester are already providing plenty of opportunities to explore those crazy technologies of the geoweb.

A need to incorporate the [Yahoo Geocoder](http://developer.yahoo.com/maps/rest/V1/geocode.html) into a new OpenLayers app has proved to be a great learning experience in the navigation of the development version of OL. <!-- more --> The [YahooGeocoder.js](http://trac.openlayers.org/ticket/1784) addin, created by OL community member, 'sbenthall', requires two prerequisites to run: A [proxy.cgi script](http://trac.openlayers.org/wiki/FrequentlyAskedQuestions#ProxyHost) as well as a Yahoo Developer API key. Why would this RESTful service require a proxy, you might find yourself asking? Well, because even though you can query it in a RESTful fashion, the data is returned in an XML shell, requiring a proxy to allow complete the XMLHttpRequest. Yahoo has a great article for novice web programmers like myself explaining the role of  Web Proxies, which can be found [here](http://developer.yahoo.com/javascript/howto-proxy.html).

A quick overview of the primary steps to add the YahooGeocoder.js addin are as follows:



	
  1. Sign up for a Yahoo APP Key to enable access to their geocoding service.

	
  2. Add the proxy.cgi script to your webserver's 'cgi-bin'. Note: When navigating to the proxy.cgi's url, you might encounter, 'access denied' errors. If you do, make sure that you have the proper permissions set for your cgi-bin directory. This can be done using the terminal command, 'chmod 755' targeting cgi-bin directory.

	
  3. Edit the proxy.cgi script to include 'local.yahooapis.com' in the list of 'allowedHosts'.

[caption id="attachment_228" align="alignnone" width="300" caption="Above: Modified 'proxy.cgi'"][![proxy_hosts](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/proxy_hosts-300x54.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/proxy_hosts.png)[/caption]

	
  4. Add the YahooGeocoder.js file to the OpenLayers 'lib/OpenLayers/Control' folder.

	
  5. Add "OpenLayers/Control/YahooGeocoder.js" to the variable array, 'jsfiles' inside the "lib/OpenLayers.js" library.

[caption id="attachment_229" align="alignnone" width="300" caption="Above: Modified 'lib/OpenLayers.js'"][![control_add](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/control_add-300x149.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/control_add.png)[/caption]

	
  6. Test the geocoder's functionality using the supplied .HTML file. (Hopefully it should work!)

[caption id="attachment_230" align="alignnone" width="300" caption="Above: Geocoder Result with Properly Formed XML Response."][![good_xml_return1](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/good_xml_return1-300x217.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/good_xml_return1.png)[/caption]


Six simple steps, but it can be challenging if you haven't tried to install any addins to the OL library before.

In the above image, the firebug window can be seen returning a properly formed XML Response, having successfully executed the geocoding function. If you enlarge the image, we can compare this to the raw XML-Response using a properly constructed query. Note in both the response captured from firebug (above) as well as the raw XML (below) the presence of the address: 510 High Street, Bellingham WA, broken down into it's individual units along with the geocoded result.

[caption id="attachment_231" align="alignnone" width="300" caption="Above: Basic XML Return"][![basic_xml](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/basic_xml-300x221.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/basic_xml.png)[/caption]

Further diving into the capabilities of firebug, we can use the DOM inspector to ensure that the various parameters required to properly execute the Yahoo Geocoder are in place. Note in the image below the presence of such necessary information as the APP ID Key, Projection, and Class, for the ygc variable. If any of these parameters happened to be incorrectly set, it would be displayed in this view.

[caption id="attachment_232" align="alignnone" width="300" caption="Above: The Firebug DOM Inspector"][![dom_inspector](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/dom_inspector-300x91.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/01/dom_inspector.png)[/caption]

I'm finally starting to appreciate the power of firebug as a development tool, which just so happens to coincide with my ability to understand it at a basic level. Hopefully as my experience in GIS Web Development grows, so will be ability to use the higher-end functions of this tool.
