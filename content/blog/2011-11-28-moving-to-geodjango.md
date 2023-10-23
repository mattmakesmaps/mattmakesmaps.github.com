---
date: 2011-11-28
title: Moving to GeoDjango
---

I've been creating a simple GeoDjango application for managing environmental sampling metadata, and it's been a lot of fun so far. I've had experience working with many different forms of metadata tracking, from spreadsheets, to wikis, to online project management tools. All of them have their ups and downs, and it seems like there is always a dealbreaker with each organizational method.

Spreadsheets are easy to edit, but lack any form of relational structure (two sets of data for the same report? i guess i'll just put two entries into the same cell).
<!-- more -->

[caption id="attachment_487" align="alignnone" width="464" caption="sometimes spreadsheets just don't work..."][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot.png)[/caption]

Wikis are cool, allow easy access to information, but are (in certain cases) a pain for folks to edit. Take the experience of table creation. Dokuwiki, a generic wiki software, requires a series of  carefully placed carrots and pipes to delineate headers and columns. A common pain comes when adding a new value to a new row, in which that value exceeds the length of any previous cell. This requires the author to expand the column header, and all previously entered rows, re-aligning the pipes and carrots. Granted as a slightly OCD GIS Analyst, the sight of a well crafted text table fills me with no less wonder then when I saw Albert Bierstadt's ["Puget Sound on the Pacific Coast"](http://www.seattleartmuseum.org/exhibit/exhibitDetail.asp?eventID=21084), but it's just darn tedious at times. Additionally, as the log of sampling events grows larger, it gets harder to manage. Dokuwiki, AFAIK provides no ways to automatically resort entire sections of pages or records in tables based on alphabetical order, which would make searching for information on a particular page much faster as content becomes larger and larger.

```bash
^ Column One                ^ Column Two                 ^
| Zee 'z' string            | This is a longer string    |
| 2nd longer string of text | 2nd short string           |
| SuperShort                | A string starting with 'A' |
```

Online project management tools are interesting as well. They allow rapid collaboration between project members, and provide template functionality, allowing for status reports on recurring workflows to be easily generated (e.g., create a template for a report, spawn off an instance of a template for each new project). The downside to these services are that: they cost money, they also may not provide a normalized way to store data, and (of most interest to myself) they probably don't allow for the storage/visualization of spatial data.

[caption id="attachment_488" align="alignnone" width="300" caption="10 megs free for one user? cool!"][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot-1-300x162.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot-1.png)[/caption]

In comes GeoDjango. Over the next few posts, I think I'll record my experiences developing an application that allows the storage of metadata, within the context of environmental sampling efforts. The goal is to provide a web application which stores both tabular and spatial data in a normalized fashion, and easily visualize both in an informative way.

[caption id="attachment_489" align="alignnone" width="300" caption="Hey look Ma', it's GeoJSON!"][![](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot-2-300x147.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2011/11/Screenshot-2.png)[/caption]
