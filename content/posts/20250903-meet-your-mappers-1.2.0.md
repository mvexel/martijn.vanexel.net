---
title: Meet Your Mappers 1.2 is out
description: Geolocation, no more log in and a clear button!
date: 2025-09-03
tags: [openstreetmap, software, programming, community]
image: https://images.rtijn.org/blog/20250903-mym120/mym-1-2-0.png
card: summary # or summary_large_image
draft: false
toc: false
aliases:
    - osm/meetyourmappers-1-2
---
I updated the [Meet Your Mappers](https://mappers.osm.lol/) tool to version 1.2.
Meet Your Mappers is a website I made a while ago that lets OSM mappers find active peers in their area. 
I use it myself to find new mappers to invite to [OSM Utah](https://osmutah.org/) meetups. 
I wrote about Meet Your Mappers in more detail [elsewhere on this blog](https://mvexel.prose.sh/20250401-meetyourmappers). 
I also talked about it at The State of the Map U.S. conference in Boston earlier this year. 
My slides from that presentation are [here](https://mvexel.prose.sh/20250524-meet-your-mappers-sotmus-2025) 
and you can watch the session [here](https://youtu.be/x1eGX54DC74?si=y6FaTNvAx7c-hX2R)<sup>youtube</sup>.

In 1.2, I addressed a few user concerns and added a couple of small features:
- I removed the need to authenticate with OSM. I initially felt this was needed because the tool exposes OSM usernames, but I actually think it's fine, and it just creates complications. Some folks could not log in and I could not figure out why.
- I added a geo-location button on the map to quickly return the map to your approximate location. 
- I added a "Clear" button to clear any drawn box or polygon and the mappers table, if loaded.
- Some dependency and Docker image updates to keep everything secure.

Happy mapping!