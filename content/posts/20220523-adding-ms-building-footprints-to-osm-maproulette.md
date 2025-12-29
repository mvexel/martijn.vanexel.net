---
title: 'adding microsoft building footprints to OSM with MapRoulette: why, and how'
date: 2022-05-23
tags: [openstreetmap, geo]
---

Microsoft [released a machine-generated dataset of building footprints for the United States some years ago](https://blogs.bing.com/maps/2018-06/microsoft-releases-125-million-building-footprints-in-the-us-as-open-data/). The footprints are derived from aerial imagery. This works well, most of the time. Where you run into problems is in rural areas, especially where there's natural features and topography that throws the machine learning off. Then the machine starts to think all kinds of things are buildings:

![](https://images.rtijn.org/buildingfail1.png)
![](https://images.rtijn.org/buildingfail2.png)
![](https://images.rtijn.org/buildingfail3.png)
![](https://images.rtijn.org/buildingfail4.png)

This is one of the reasons why **blindly importing this data into OSM is a bad idea**. MapRoulette comes to the rescue, it can serve these footprints one at a time, and OSM mappers can work together to decide if a geometry does in fact represent a building and could be added to OSM. Here's how:

### Target Area

First we choose a target area. If you want to make the MapRoulette Challenge reasonably sized, I would suggest to do a county-sized area. For my demo purpose, I choose Wayne County in Utah. This is a great example, because it is a rural county with some small towns and lots of interesting topography to throw off machines. The examples above are all taken from Wayne County.

### Download The Data

We need three datasets to prepare the buildings as a MapRoulette Challenge:

1. The Microsoft Building Footprint data, which you can download as statewide files [from Github](https://github.com/Microsoft/USBuildingFootprints). We downloaded the Utah file, which is a 306 MB GeoJSON.
2. A county boundary file to select only those geometries from the Building Footprints file that are within Wayne County. We download a Utah county boundaries file [from Census](https://www.census.gov/geographies/mapping-files/time-series/geo/cartographic-boundary.html).
3. The currently existing building footprints for Wayne County in OSM. We download these using [an Overpass Query](https://overpass-turbo.eu/s/1iIk). 

### Pre-process in QGIS

This section assumes some familiarity with QGIS.

We load all three downloaded datasets into QGIS. This should look something like:

![Utah in QGIS](https://images.rtijn.org/utah_buildings_qgis.png)

Wayne County is selected here. We then use the `Extract By Location` processing function to extract the building footprints that are within Wayne County:

![Buildings extraction in QGIS](https://images.rtijn.org/utah_buildings_clip.png)

Lastly we discard those footprints that are overlapping any building footprint already in OSM:

![Buildings extraction in QGIS](https://images.rtijn.org/utah_buildings_disjoint.png)

We save this result as a GeoJSON file.

### Pre-process in JOSM

Next, we need a little bit of processing in JOSM. We load the GeoJSON file from the previous step into JOSM:

![Buildings in JOSM](https://images.rtijn.org/utah_buildings_josm_1.png)

We then use the Find function to select each way:

![Finding ways in JOSM](https://images.rtijn.org/utah_buildings_find.png)

(You can't just 'Select All' in JOSM because that would select both the ways and all individual nodes.)

With all buildings selected, we can simply add the `building=yes` tag to all of them at once. We then save the layer as a `.osm` file.

### Creating the MapRoulette Challenge file

Using the `.osm` file, we can use the `mr-cli` tool to convert this into a MapRoulette Cooperative Challenge GeoJSON file. This is described in detail [in the MapRoulette Documentation](https://learn.maproulette.org/documentation/creating-cooperative-challenges/). In short, we use the command 

`mr coop change --out msbuildings_waynecounty_challenge.geojson msbuildings_waynecounty_notinosm.osm`

The resulting GeoJSON file can be read by MapRoulette. MapRoulette will detect that this is a Cooperative Challenge GeoJSON and will create the Challenge accordingly.

### Creating the Challenge in MapRoulette

The final step is to create the MapRoulette Challenge. This is an interactive process done on maproulette.org. You feed it with the created GeoJSON and instructions for the mapper, and you're good to go! You can learn more about creating challenges from the [MapRoulette documentation](https://learn.maproulette.org/). There you will find a number of articles and screencasts on the topic.

### Result

The Challenge created using the steps above is [here](https://maproulette.org/challenge/27617). (This is an "undiscoverable" Challenge, meaning that it will not show up in the Challenge discovery on maproulette.org, but you can still get to it via a direct link.)

![Challenge in MapRoulette](https://images.rtijn.org/utah_buildings_challenge.png)

*I cross-posted this blog post [on my OSM Diary](https://www.openstreetmap.org/user/mvexel/diary/399229)*