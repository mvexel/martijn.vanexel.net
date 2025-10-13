---
title: 'openstreetmap how-to: clean up after tiger'
date: 2020-01-14
---

I’ve been working on a new version of the [TIGER Battlegrid](https://wiki.openstreetmap.org/wiki/TIGER_Battlegrid), but [haven’t had a lot of time](https://www.flickr.com/photos/rhodes/49385909318/in/dateposted/) to make progress lately, so in the mean time I’ve been thinking about quick ways to help mappers clean up after TIGER. I have a couple of ideas. One involves MapRoulette that will require a little bit of time to execute. The other one is for JOSM users; just follow the steps below!

First, open up the Download dialog in JOSM, and switch to the Overpass tab. This lets you selectively download data into JOSM based on an Overpass query. We will use this to only download residential ways that have not been edited since 2009, and have no name.

Plug in the following query:

```
way
  [highway=residential]
  [!name]
  (if(timestamp() < "2010-01-01T00:00:00Z"))
  (area:3600036074);
out;
>;
out meta;
```

Note the `(area:3600036074);`. This is Overpass lingo for: ‘limit my query to the area defined by relation 36074’. This relation defines [Erie County, NY](https://www.openstreetmap.org/relation/36074). You probably want to retrieve data for a different county. You can find the relation ID for any administrative boundary by searching for the area by name on the OSM web site.

If you would rather draw a bounding box and download the stale road data for that area, you need to adapt the query as follows:

```
[bbox:{{bbox}}];
way
  [highway=residential]
  [!name]
  (if(timestamp() < "2010-01-01T00:00:00Z"));
out;
>;
out meta;
```

![JOSM TIGER query](https://images.rtijn.org/josm-tiger-query.jpg)

Execute the download request and you will end up with an area that is likely littered with little road fragments.

![JOSM TIGER loaded](https://images.rtijn.org/little-road-fragments.jpg)

All of these need a look, so don’t bite off more than you can chew.

We will use the genius [Todo List](https://wiki.openstreetmap.org/wiki/JOSM/Plugins/TODO_list) JOSM plugin to organize the work ahead of us. If you don’t have it, install it first. Then, select all the ways using JOSM’s Find dialog. Use the syntax `type:way` to select all the ways but not the nodes.

![JOSM ToDo list screenshot](https://images.rtijn.org/todo-list.jpg)

With all the ways selected, go to the Todo List plugin and click ‘Add’ to add all the ways to your Todo list.

Now you’re ready to start cleaning up. Double-Click on the first item in the list to zoom to it. There’s a number of things to check for each way:

* Is the way actually a `residential` class road? If it’s not in a built-up area and / or there are no residential structures on the road, it is probably `unclassified`. If it’s unpaved and doesn’t look like a road that is usable by a normal 2WD passenger car, it’s likely a `track`. In some areas, a lot of the ways are actually driveways, `highway=service;service=driveway`. Be sure to familiarize yourself with the definitions of the various road classes, and ask around if you are unsure.
* If the road does in fact meet the criteria for `residential`, it needs a name. Activate the `TIGER Roads 2019` layer or another available source to find the name, and add it.
* If the way has `tiger:*` tags, you can opt to remove them altogether, or at least change `tiger:reviewed` to yes.
* If the way is poorly aligned to the best available aerial imagery, improve the alignment.

![JOSM TIGER alignment screenshot](https://images.rtijn.org/tiger-alignment-screenshot.jpg)
*Working on a stale residential road. This is in fact a `service=driveway`*

It may be helpful to download additional OSM data around the way you are looking at, especially if you are moving nodes around to improve alignment. The way is likely connected to other ways that were not included in our limited Overpass query!

When you’re satisfied, upload the changes. Optionally use the hashtag `#tigercleanup`. Then, in the Todo List plugin, click on ‘Mark’ to mark the item as done. Todo List will remove the item from your list and automatically pan the map to highlight the next way!