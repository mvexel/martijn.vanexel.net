---
title: 'technical deep dive: creating the maproulette tesla parking lot challenge'
date: 2019-12-27
---

In a previous post, I talked about a new MapRoulette challenge to help identify surface parking lots that need more detailed mapping of roads and aisles within the lot.

In this post, I want to dive a little deeper into how a Challenge like that is made.

## Challenges in MapRoulette

MapRoulette is the micro-tasking platform for OpenStreetMap. On it, mappers can select any one of millions of Tasks and make a small but meaningful contribution to improving the quality of OSM. Tasks are grouped in Challenges that cover a specific topic, like updating business phone numbers, aligning roads, etcetera.

MapRoulette is designed to make creating Challenges as easy as possible. A graphical interface takes you through the creation process step by step. A key part of any Challenge are the Task locations. When you create a Challenge, you can upload these as a GeoJSON file.

This post will focus on how I filtered and queried existing OSM data to get the locations of parking lots that lack the road detail.

Before I start, I’ll mention that there are probably many other ways to get to the result I am after. The steps I describe here are based on my experience and the tools I know best. I would love to hear how you would accomplish a similar result using other tools, like QGIS.

## Step 1. Get The Data

OpenStreetMap data is available from many places, including planet.osm.org, Metro Extracts, BBBike and Geofabrik. I want to limit the Challenge to California, because Tesla sells the most cars there, and to limit the number of Tasks. Geofabrik offers U.S. state level extracts, so I download the osm.pbf file from there. Thanks for providing this service, Geofabrik!

## Step 2. Filter The Data

The downloaded file is large, and I only need parking lots. I find osmium to be the best tool to quickly filter OSM data files.

The question I want to answer is: “Where are surface parking lot areas in OSM data that are not covered by parking aisle ways?” To answer that question I need to filter those two feature types, parking lot areas and parking aisle ways, from the data:

```
osmium tags-filter \
  -o cali-parking.osm.pbf \
  --overwrite \
  california-latest.osm.pbf \
  w/service=parking_aisle a/amenity=parking
```

If you’re interested in learning about the osmium tag filter syntax used, have a look at the [documentation](https://docs.osmcode.org/osmium/latest/osmium-tags-filter.html).

## Step 3. Load into PostGIS

The best way I know to perform a spatial analysis to answer the question “Which parking lots do not have parking aisles running through them yet?” is PostGIS. QGIS would probably be another good option. If you know how, let me know!

We use osmosis to load the filtered OSM data. After [creating the empty database](https://wiki.openstreetmap.org/wiki/Osmosis/PostGIS_Setup#Create_and_Initialise_Database), this is as easy as

`osmosis --rb cali-parking.osm.pbf --wp database=osm`

Depending on your PostgreSQL setup, you may need to [provide credentials](https://wiki.openstreetmap.org/wiki/Osmosis/Detailed_Usage_0.47#--write-pgsql_.28--wp.29).

## Step 3. The Empty Lots Query

We now have the data where we want it and are ready for the spatial query:

```
create table lots as (
  select * from ways where 
    tags?'amenity' 
    and st_isclosed(linestring));

alter table lots add column geom geometry;

update lots set geom = ST_MakePolygon(linestring);

create table aisles as (
  select * from ways where 
    tags?'service');

create index idx_aisles on aisles using gist(linestring);
create index idx_lots on lots using gist(geom);

create table emptylots as 
  select id, geom from lots where 
  id not in (
    select distinct l.id from 
    lots l join aisles a on 
    st_intersects(l.geom, a.linestring)) 
  and st_area(geom) > 0.000001;
```

## Step 4. Get The GeoJSON

MapRoulette expects a GeoJSON file defining the Task geometries. We are going to rely on the [vastly improved ST_AsGeoJSON function](https://postgis.net/docs/ST_AsGeoJSON.html) available in PostGIS 3.0.0 and later. If you’re stuck with PostGIS 2.x, you will need to do some creative querying that is beyond the scope of this guide. You can also use QGIS to load the `emptylots` table and then export it to GeoJSON.

```
\copy (SELECT json_build_object('type', 'FeatureCollection', 'features', json_agg(ST_AsGeoJSON(l.*)::json)) FROM (SELECT ST_ForcePolygonCW(geom) geom, id FROM emptylots) l) TO '/home/mvexel/osm/emptylots.geojson';
```

Even though we used the `ST_ForcePolygonCW` function to force the correct orientation for the polygons (the “right-hand-rule”), MapRoulette is not happy with the GeoJSON in this form, so we need to use the `geojson-rewind` tool to fix it.

To install `geojson-rewind`:

```
npm install -g geojson-rewind
```

Then simply run it on the GeoJSON file we extracted from PostGIS.

```
geojson-rewind ~/Desktop/emptylots.geojson > ~/Desktop/emptylots-fixed.geojson
```

## Step 5. Create The MapRoulette Challenge

Now we have all the ingredients to create the MapRoulette Challenge. The [MapRoulette documentation](https://github.com/osmlab/maproulette3/wiki) has a [link to a step-by-step guide](https://www.openstreetmap.org/user/mvexel/diary/46863). The guide’s screenshots don’t reflect the latest MapRoulette design, but you should be able to follow along and create the Challenge using it.

Have fun and happy mapping!