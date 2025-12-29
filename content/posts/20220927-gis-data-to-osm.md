---
title: "adding high quality gis data to osm, one feature at a time"
tags: [openstreetmap]
date: 2022-09-27
---

*This is a cross-post from my OpenStreetMap Diary*

RapiD is an alternative to the OpenStreetMap iD editor that adds the ability to easily add data from approved external sources to OSM. Chances are that you—like me—know RapiD from the MapWithAI initiative, Facebook / Meta's effort to publish road geometry derived from aerial imagery using machine learning. RapiD started out to support this effort; a fork of the iD OSM editor, RapiD added a layer to display the ML-detected road segments, and a nice user interface to add these segments to OSM, connecting them with the existing road network where possible.

![rapid in ml roads mode](https://images.rtijn.org/rapid-ai-roads.png "RapiD showing ML generated roads in Kazakhstan")

I am interested in RapiD not so much for the AI-generated roads (however nifty), but for the functionality they added later to assist in adding high quality GIS data from official sources to OSM. This is not new; the RapiD folks already demonstrated these capabilities at MappingUSA in the spring of 2020 ([video](https://www.youtube.com/watch?v=0NBnmSQwHF4)). In a [collaboration with Esri that was kicked off in 2020](https://www.esri.com/arcgis-blog/products/arcgis-living-atlas/mapping/arcgis-data-support-in-osm-editors/), RapiD now features dozens of open data sources from local and state GIS agencies throughout the United States that opted in to Esri's [Community Data Sharing](https://www.esri.com/arcgis-blog/products/arcgis-living-atlas/mapping/community-maps-data-sharing/).

If you've been following my OSM activity over the years, you may know that I have a strong interest in involving GIS agencies and the awesome data they produce in OSM. To that end, I've [given](https://eventil.com/events/the-39th-quarterly-salt-lake-gis-users-group-slug-meeting) [talks](https://www.slideshare.net/mvexel/the-golden-age-of-gis-keynote-for-gis-day-2019) for government GIS groups and GIS students, I've [collaborated](https://gis.utah.gov/introducing-open-source-places/) with the Utah state GIS agency, UGRC, to create an OSM-derived places dataset to support 911 and other services, and wrote more than once about [how to clean up after](https://ma.rtijn.org/20200303-maproulette-utah) well-intentioned but poorly executed government GIS data injections into OSM. I created—and continue to work on—MapRoulette, a micro-tasking tool that was created to help clean up after a GIS data import, and now supports a lot of efforts to improve OSM based on official GIS data. ([Here's a recent example based](https://maproulette.org/browse/challenges/28364) on official bus stop data in Germany.)

I've been adding GIS data from various agencies here in Utah to OSM using RapiD and having fun doing it. It takes the tedium out of adding things like building footprints to OSM, while still giving the satisfaction of manually adding and verifying every feature you add to the map, and knowing  that the data is properly vetted and licensed appropriately. Here's how I go about it.

It all starts with adding a GIS data source to RapiD. There is a list of data sources that are available via the Esri open data program [on the OSM Wiki](https://wiki.openstreetmap.org/wiki/Esri/ArcGIS_Datasets), but RapiD itself has a convenient search interface:

![RapiD open data search interface](https://images.rtijn.org/rapid-search.png "Seacrhing in RapiD")

I can simply click 'Add Dataset' for an open dataset that I would like to work with, and it will be added as a layer to the map. There's also a link to the Esri source page for each dataset. I would like to work with the building footprints from the City of Ogden, so I add this one to my RapiD layers. Using the layers dialog, I can then assign the layer a custom color and center the map on the layer extent:

![layer management in RapiD](https://images.rtijn.org/rapid-manage-datasets.png "Manage your layers in RapiD")

Centering the map on Ogden, I can now see the City GIS data and the existing OSM data together. Notice that there's no City GIS data where OSM buildings already exist:

![gis data in RapiD](https://images.rtijn.org/rapid-ogden.png "Inspecting City GIS data layer in RapiD")

I can now add a building footprint from the City GIS data to OSM by selecting it, comparing it carefully with best available imagery and other open sources (think recent KartaView / Mapillary images, or local knowledge), and pressing `A` to add the feature to the OSM layer. It will then turn from green to the standard iD to...whatever you call the color that indicates a building in iD :)

![animation of rapid feature add](https://images.rtijn.org/osm/rapid-add-feature.gif)

I can now repeat this as often as I like 🙂. Try it out! The aforementioned OSM wiki page has some words of caution that you should heed when you do though: 

![wiki warning](https://images.rtijn.org/rapid-wiki-warning.png "Warning on the OSM wiki")

Let the RapiD team know about your experience! [RapiD's Github](https://github.com/facebookincubator/RapiD) README has ways to get in touch. 

As always, happy mapping! 

*disclaimer: I work on OSM-related projects for Kaart at Meta, who fund the development of RapiD. I'm writing this post on personal title.*