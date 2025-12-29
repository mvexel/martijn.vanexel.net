---
title: announcing maproulette challenges for fixing up tiger in utah
date: 2020-03-03
---

I [wrote a how-to recently](/20200114-openstreetmap-tiger) on “Cleaning up” after the OpenStreetMap TIGER import. As a reminder, here’s what TIGER data used to look like when it was imported into OSM in 2008:

![tiger in 2008](https://images.rtijn.org/tiger-2008.jpg)

Fortunately, the OpenStreetMap community has fixed most of the alignment and road classification issues connected to the TIGER import in the past 12 years. (With some help from tools like [Battlegrid](https://wiki.openstreetmap.org/wiki/TIGER_Battlegrid), which I wrote when at Telenav.) Yet, problems remain. In that previous blog post, I outlined an easy to follow set of steps to identify and start fixing problems in your area of interest.

Writing a blog post does not fix map problems. And most people reading this will be casual OSM contributors at best, and are not interested in following a bunch of steps that involve JOSM and plugins, to start fixing map problems.

That is why we have MapRoulette! MapRoulette gives you ready-to-fix map issues with clear instructions telling you what to do to fix them. Individual tasks don’t take more than a minute or two to fix. Ideal for casual mappers, but not to the exclusion of power mappers, many of whom have told me that they love to use MapRoulette as a tool to organize their own mapping work as well as that of their mapping group / community.

I took the Overpass query from the previous post and used it to create [MapRoulette challenges for each county in Utah](https://maproulette.org/browse/projects/39560). You can follow the link to the MapRoulette project to start helping to fix these roads, or read on below the screenshot for a quick recap of how I created the Challenges. Happy mapping!

![Clean up TIGER challenge screenshot](https://images.rtijn.org/clean-up-tiger.png)

## Challenge Creation

First, I identified the OSM IDs for all county boundary relations in Utah using this Overpass query:
    
```
[out:csv(::id,name)];
relation[admin_level=6](area:3600161993);
out;
```

That gave me the following results:

``` 
@id name
416536  San Juan County
445605  Wayne County
445606  Grand County
1411396 Daggett County
1411399 Uintah County
1416619 Rich County
1416621 Summit County
1742078 Box Elder County
1742080 Cache County
1744354 Beaver County
1744355 Carbon County
1744356 Davis County
1744357 Duchesne County
1744358 Emery County
1744359 Garfield County
1744360 Iron County
1744361 Juab County
1744362 Kane County
1744363 Millard County
1744364 Morgan County
1744365 Piute County
1744366 Salt Lake County
1744367 Sanpete County
1744368 Sevier County
1744369 Tooele County
1744370 Utah County
1744371 Wasatch County
1744372 Washington County
1744373 Weber County
```

I created the first Challenge by replacing the area identifier in the original Overpass query with the Overpass-adjusted area id for the first county:

``` 
way
  [highway=residential]
  [!name]
  (if(timestamp() < "2010-01-01T00:00:00Z"))
  (area:3600416536);
out;
>;
out meta;
```

Note that I also replaced
    
```
out;
>;
out meta;
```

with simply
    
```
out geom;
```

This is to prevent MapRoulette from interpreting the nodes that make up the ways as tasks, instead of the ways themselves. The original query returns the ways, without geometry, in the first `out` statement, then the `>` triggers a recursive query for all the nodes that are members of those ways, and returns their geometries in the `out meta` statement. Those nodes are then interpreted as separate tasks, which is not what we wanted. `out geom` on the other hand, [will make overpass output full line geometry](https://wiki.openstreetmap.org/wiki/Overpass_API/Overpass_QL#out), thus creating one task for each way.

*Postscript: Technically this is not ‘TIGER Fix-up’—the query is just targeting ‘any residential way in OSM that has no name and has not been edited since 2009’. But it is a simple query that is a decent proxy for TIGER fix-up. There is an entire OSM wiki page devoted to more specific queries you may use.*