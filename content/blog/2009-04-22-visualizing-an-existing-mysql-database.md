---
date: 2009-04-22
title: Visualizing An Existing MySQL Database
---

So I've been working for about a month with a fairly-normalized (53-table) database in which I draw out all kinds of tabular information, and display it in a spatial context. This has required the numerous multiple table joins, with all kinds of weird relationships... you know, the kind that usually don't work out very well? <!-- more -->

In any event, my SOP for handling these queries was to submit sample data through the codeigniter site that our project's web developer has been courageously firing away at. In this sense, I'd sort-of trace the flow of new information through the various tables of the database, monitoring the information stream as best as I could. I thought to myself, that there has to be a better way to handle this stuff! In comes the [MySQL Workbench](http://dev.mysql.com/workbench/). This handy tool from the [MySQL Dev Zone](http://dev.mysql.com/) apparently comes in two flavors: FOSS and commercial.

The free version served my visualization needs perfectly. The layout of the program is very solid. I was easily able to take an SQL export of the existing database and import it into the Workbench, through a tool they call 'Reverse Engineer MySQL Create Script'. Once the schema has been injected into the program, a model can be automatically created containing all of the tables as well as relationships. The auto-layout feature however, leaves a lot to be desired.

[caption id="attachment_293" align="alignnone" width="300" caption="Above: Automatic Layout Results, Snazzy!"][![Above: Automatic Layout Results, Snazzy!](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/initial_layout-300x189.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/initial_layout.jpeg)[/caption]

After about twenty-minutes of fooling with the table graphics, a usable layout can be produced. One feature that I think is really convenient, but will never use, is the automatic setting of the diagram width and height based on numbers of pages. This is useful for those who need a quick-print out of their database for whatever reason.

[caption id="attachment_292" align="alignnone" width="300" caption="Above: Workbench w/ Completed Diagram"][![Above: Workbench w/ Completed Diagram](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/workspace-300x187.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/workspace.jpeg)[/caption]

The real benefit for me however, is the automatic highlighting of key values linking tables together. I'm now able to quickly work my way from the table I need to get to, drilling backwards until I see the table I need to start with.

[caption id="attachment_294" align="alignnone" width="300" caption="Above: Automatically Highlight The Key Fields Between Two Tables."][![Above: Automatically Highlight The Key Fields Between Two Tables.](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/linking_tables-300x147.jpg)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2009/04/linking_tables.jpeg)[/caption]
