---
title: 'create your own maproulette challenge: updating deprecated tagging'
date: 2020-02-22
---

I am really proud of how far MapRoulette has come. It is now really easy to create Challenges mappers can work on together to improve OpenStreetMap. Many OSM groups already use MapRoulette to coordinate mapping activities—both volunteer communities as well as corporate mapping teams. Read for example [this recent blog post by Monica Brandeis of Critigen](https://medium.com/critigenopensource/improving-osm-data-one-virtual-mapathon-at-a-time-cb64e3445f69), where she explains how Critigen used MapRoulette to host Virtual Mapathons at their various corporate office locations. (The Challenges they created are all completed, but there’s [many more](https://maproulette.org/browse/challenges?sort=popularity) to choose from!)

I am also starting to use MapRoulette to organize work for our local OSM group here in Salt Lake City. My hope is that this will draw in more new mappers, by showing them small but meaningful ways to start contributing to the quality of the map locally. I also hope that having a set of local Challenges will strengthen the sense of community by having shared mapping goals and a dashboard to see how well we’re doing achieving them.

![maproulette challenge screenshot](https://images.rtijn.org/maproulette-screenshot.png)

As a start, I created [a Challenge to update deprecated tagging](https://maproulette.org/browse/challenges/12681). The `name_1` tag is still in use on a large number of ways. The value is supposed to be an alternate name for a road, but it has been deemed deprecated in favor of `alt_name` and other more specific `name` tag variants.

The easiest way to build a Challenge with these is by using Overpass as a source. To identify all ways that have a `name_1` tag, you can use this Overpass query:

``` 
area[name="Salt Lake City"]->.a;
way[name_1](area.a);
out meta geom;
```

You can feed this query into a new MapRoulette Challenge using the Challenge Create wizard. The tasks will build automatically, and you can refresh them as needed.

![maproulette overpass input screenshot](https://images.rtijn.org/overpass-screenshot.png)

You could easily change the Overpass query to highlight OSM objects with a [different deprecated tag](https://wiki.openstreetmap.org/wiki/Deprecated_features#Deprecated_features) on them, and create a Challenge for those as well! 