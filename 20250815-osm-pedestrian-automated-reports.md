---
title: Pedestrian Accessibility in OpenStreetMap
description: Developing a detection algorithm for pedestrian infrastructure map completeness in OpenStreetMap data
date: 2025-08-15
tags: [openstreetmap, pedestrian, accessibility, gis, analysis]
image: https://images.rtijn.org/blog/20250820-pedestrian/image2.jpg
card: summary # or summary_large_image
draft: false
toc: false
aliases: 
---
*If you are interested in custom OSM data quality analysis with actionable results that can be used to engage the OSM mapping community, or guide your own teams to solve specific quality issues you care about for your use case, you can [hire me](https://www.linkedin.com/in/mvexel/)\!*

One area where OpenStreetMap (OSM) really shines in United States cities compared to other, proprietary maps is the quality of pedestrian infrastructure. OpenStreetMap United States has a dedicated [Pedestrian Working Group](https://openstreetmap.us/news/2024/02/pedestrian-working-group/) that meets regularly to talk about ways to further improve the quality of pedestrian infrastructure and engage the mapping community. To learn more about their work, I recommend watching Chad Blevins’ talk at the recent State of the Map U.S. conference.

One of their accomplishments is a very clever classification system that categorizes pedestrian infrastructure quality for a city as Bronze, Silver or Gold, based on the level of detail available, such as the presence of surface type and quality, crossing detail and curb cut detail. They created a [guide](https://wiki.openstreetmap.org/wiki/Foundation/Local_Chapters/United_States/Pedestrian_Working_Group/Guide#Mapping_Guidelines) that goes into great detail about how to map the features and attributes needed to reach each level.

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image10.jpg)

# **OpenStreetMap in Salt Lake City**

I run a local OSM group in my home town of Salt Lake City. We hold monthly meetings where mappers come together to share, learn and map. We recently held a special Map Night to focus on sidewalks and crossings. This proved a popular theme: 10 people showed up and we spent an evening mapping pedestrian features together.

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image7.jpg)

This event got me really excited to dive deeper into this question of quality. Could I run an analysis that could point our group, and mapping groups everywhere, to specific intersections where work still needs to be done?

# **Designing a Pedestrian Infrastructure Quality Algorithm**

I have been involved in OSM for almost twenty years and worked on data quality analysis for a good part of that time, so I think I should be able to put something together\! Let’s look at the problem space in a bit more detail to see what we need to measure. Let’s look at an intersection that would represent the “Gold Standard”

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image2.jpg)

This intersection has it all:

* Sidewalks on both sides of all roads connecting to this intersection.  
* Surface tags (like surface=concrete) on all sidewalks and crossings  
* Crosswalks marked with dedicated tags on both the ways and the nodes where the pedestrian geometries intersect with the main road features  
* Curb cuts separately mapped with detailed attributes

At the other extreme we have intersections that have no pedestrian features mapped at all:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image8.jpg)

My analysis focused on identifying intersections like this one, that obviously need more detail for pedestrian navigation. I implemented an automated GIS analysis using Python, Geopandas, OSMnx, NetworkX and sklearn that consists of several steps

* **Crossing Count:** Count highway=footway \+ footway=crossing ways within a (configurable) buffer, then group nearby segments to avoid overcounting  
* **Curb Cut Count:** Count accessible curb cuts from nodes (kerb=lowered/flush) and ways (barrier=kerb \+ kerb=lowered/flush)  
* **Sidewalk Detection:** Identify highway=footway \+ footway=sidewalk ways OR roads with sidewalk=both/left/right

I then classify intersections into a number of buckets:

* **Needs Curb Cuts:** Has crossings but insufficient curb cuts  
* **Missing Crossings (No Sidewalks):** No crossings, no sidewalks detected  
* **Missing Crossings (Has Sidewalks):** No crossings, sidewalks detected nearby  
* **Complete:** Rich detail including crossings and curb cuts present

I wrapped this analysis in a Python script with a number of parameters that can be tweaked based on the local situation or false negative tolerance.

# **Results**

Running this analysis on Salt Lake City takes 50 seconds on my M1 MacBook pro, and that includes OSMnx retrieving the network graph.

In spite of our local mapping community’s intense focus on pedestrian infrastructure, it’s clear (but unsurprising) that there’s still work to do\! Of 3003 intersections considered:

* **744 are Complete**  
* 464 have no sidewalk information at all  
* 579 have sidewalks but no crossing information  
* 1216 need more curb information

## **Missing Sidewalks**

Here are the 464 intersections in Salt Lake City that, according to my analysis, have no sidewalk information at all.

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image3.jpg)

If an intersection does not have sidewalk information, that could just mean that no sidewalks exist, like here:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image4.jpg)

In that case, the road features should still be tagged with sidewalk=no. Many cases I looked at are like this.

There are false positives too, like this intersection that has sidewalk and even curb information that was not picked up by my analysis algorithm, possibly because the road is quite wide and the sidewalks fall outside the default buffer. Needs investigation\!

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image13.jpg)

## **Missing Crossings**

This is a map of the 579 intersections that have sidewalks around, but no complete crossing information:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image12.jpg)

Here, we find some false positives as well, such as this situation here:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image1.jpg)

No obvious crossing opportunity for pedestrians exists at this intersection, so the representation in OSM is in fact correct.

Many intersections identified as having sidewalks but no crossings are actually in need of mapping work, however:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image8.jpg)

## **Missing Curb Cuts**

Here is a map of the 1216 intersections that still need detailed curb cut mapping work:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image6.jpg)

Such as this colorful intersection here:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image11jpg)

# **Conclusions and Further Work**

I am happy with this analysis result. It required a day of coding and iteration, and the result is a jumble of spaghetti code that needs clean up. I will iterate on it some more. The results could be better at complex intersections where a separated highway is involved, like here:

![alt text](https://images.rtijn.org/blog/20250820-pedestrian/image13.jpg)

This complex intersection was not considered at all, meaning it was not identified by my algorithm as an intersection in the first place. Some feeble attempts to fix this led to new false positives, so I gave up on that for now now, to revisit at a later point.

The results of this analysis is highly actionable for local (and remote\!) mappers. The output of the script is a bunch of GeoJSON files that can be used by mappers and mapping groups in a variety of ways:

* Create MapRoulette tasks for each intersection that needs work, for easy and efficient collaboration between mappers.  
* Load files directly into JOSM or iD to identify intersections to focus mapping work  
* Use in QGIS or ArcGIS for deeper analysis

I am providing the output files for Salt Lake City as a [zip file download](https://images.rtijn.org/osm/pedestrian_analysis_results.zip). I am happy to run the analysis for your city if you are a volunteer OSM mapper or run a local mapping group.