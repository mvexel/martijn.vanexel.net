---
title: Quadkeys are sometimes hard to find
date: 2024-09-13
tags: [gis, mapping, geo]
description:
---

[Quadkeys](https://learn.microsoft.com/en-us/bingmaps/articles/bing-maps-tile-system) are Bing Maps's way of subdividing a Web Mercator map of the world into tiles. It is a simple quadtree-like system where each tile at a spcific zoom level has exactly four children:

![bing maps quadkey visualization](https://learn.microsoft.com/en-us/bingmaps/articles/media/5cff54de-5133-4369-8680-52d2723eb756.jpg)

I find myself needing to convert map location on earth in longitude / latitude to a Bing quadkey sometimes, and I found there is no easy web tool to do this, so I wrote one: [What the Quad](https://quad.osm.lol/):

![what the quad screenshot](https://images.rtijn.org/2024/whatthequad.png)


As a bonus, it will also give you the z/x/y coordinates, a different hierarchical tile indexing system popularized by OpenStreetMap. The OSM wiki has [a decent description](https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames) of this system.