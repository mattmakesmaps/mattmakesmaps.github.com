---
date: 2009-01-31
title: 'Google Motion Charts: From Data To Information '
---

I've been playing with the motion chart gadget in Google's AJAX API Playground recently, and have found it to be an extremely interesting tool to use. In order to produce data rich charts, all that is necessary is to import a tabular datasheet into google docs and call on it within a javascript function. <!-- more -->

The spreadsheet itself requires:



	
  * The first column be entity data (place names, events, etc.)

	
  * The second column to be formatted as 'Date'

	
  * The third and following columns to contain your chart-worthy data.


A simple example can be found below depicting historical weather station information for three cities in the Western United States. This information was downloaded from the [Western Regional Climate Center](http://www.wrcc.dri.edu/) and imported manually into a [google docs spreadsheet](http://spreadsheets.google.com/ccc?key=pPDD5D7AqVH0jefmgJwF9OQ). As we can see, we have a variety of different ways to explore the data, and hopefully, synthesize them into useful information for decision makers in a best case scenario.   



  


Above: Markers indicate the cities in which weather station data was collected.

  


  


Above: Google Motion Chart Gadget. NOTE: The data are actually compiled monthly averages from 1971-2000. For the sake of simplicity however, i fed the date column in a format of 'Jan-1' which turns out automatically appends the current year. The proper date should read for example, '1/1' rather than '1/1/09'.

In any event, in a data rich GIS environment, we can easily succumb to 'data overload'. This creates a barrier for decision makers in understanding their situations, and as such can lead to poor planning. Through the careful use of data visualization tools such as this, however, decision makers can be empowered to quickly analyze data in a variety of ways, creating useful information on-the-fly and without the use of specialized GIS technical know-how.

At least I think that's how it works.
