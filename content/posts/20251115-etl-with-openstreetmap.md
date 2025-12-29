---
title: "ETL With OpenStreetMap: Simple Things Are Simple, But Then What?"
date: 2025-11-15
description: OpenStreetMap data comes to you unfiltered, raw. OSM, being a crowdsourced map, gives you no guarantees of completeness, accuracy, or generally fitness for use. It's up to you to design a data pipeline that gets you the geodata you actually need.
tags: [openstreetmap, gis, database, postgresql, geo]
---

OpenStreetMap (OSM) data comes to you unfiltered, raw. OSM, being a crowdsourced map, gives you no guarantees of completeness, accuracy, or generally fitness for use. It's up to you to design a data pipeline that gets you the geodata you actually need. Entire teams at large companies have broken their collective brains on the multitude of challenges faced when trying to sanitize OSM data. Fortunately, for a lot of geospatial systems I've helped build, it proves to be entirely reasonable to keep things simpler than you might expect. I like to reason about systems from the simplest possible solution outward, rather from enterprise architectures inward. I like to bring a toolkit of well documented, battle-tested open source geospatial software to design and build systems that fit the job at hand rather than industry-dictated "cloud best practices".

One of the tools I really enjoy incorporating into any OSM ETL pipeline is `osm2pgsql`, or o2p as I like to call it. It's a swiss army knife for OSM data transformation and loading. Together with the `osmium` tool, it can handle the majority of OSM ETL projects I have been involved with. Both o2p and `osmium` bear the hallmarks of good software that I care a lot about: adhering to Alan Kay's "simple things should be simple" [principle](https://www.quora.com/What-is-the-story-behind-Alan-Kay-s-adage-Simple-things-should-be-simple-complex-things-should-be-possible), having great documentation, being open source and having a supportive community of users.

In this post, I hope to demonstrate that o2p is easy to get started with for someone with some technical background, while offering a comfortable glide path to more complex transformations.

Let's get started!

## Preparation

I will do all of the below on my own Apple laptop. If you're on Linux, the only part that's different is setting up PostgreSQL. If you're on Windows, I recommend using the [Windows terminal](https://learn.microsoft.com/en-us/windows/terminal/) in combination with WSL.

For Mac, the easiest way to get PostgreSQL and PostGIS running is by installing [Postgres.app](https://postgresapp.com/). It comes with recent PostgreSQL and PostGIS versions and you don't have to struggle with roles and permissions. A passwordless user with the same username as your login user will be created, and the PostgreSQL client tools will be immediately available in your terminal.

Once PostgreSQL is up and running, let's create a database for our imported data.

```bash
createdb osm
```

Then, add the needed extensions.

```bash
psql -d osm -c 'create extension postgis'
psql -d osm -c 'create extension hstore'
```

(We don't technically need `hstore` for this tutorial, and I've actually moved away from it in favor of `jsonb` columns.)

Verify that the database exists, `psql -d osm` should get you into the database and `\dx` at the database prompt should show the extensions installed.

Next, install o2p. Homebrew has the most recent version, 2.2.0 currently, so `brew install osm2pgsql` is the quickest path. MacPorts [has 2.2.0](https://ports.macports.org/search/?q=osm2pgsql&name=on) as well.

```bash
[~]$ brew info osm2pgsql
==> osm2pgsql: stable 2.2.0 (bottled), HEAD
OpenStreetMap data to PostgreSQL converter
https://osm2pgsql.org
Installed
```

We will also need some OSM data. o2p can process the entire planet efficiently (given ample system resources), but for this demo we will download a small section of the city of Bogotá, Colombia. For small areas, I like to use [OSM Slice](https://slice.openstreetmap.us/#1.27/0/0): you get near-live data, you can define your area of interest very flexibly, and it is very quick for small areas.

## Loading Data

With that data parked as `bogota.osm.pbf`, let's use the simplest possible o2p database import command just to confirm that it works.

```bash
[~/osm2pgsql-demo]$ osm2pgsql --create --slim --database osm bogota.osm.pbf
2025-11-15 10:12:46  osm2pgsql version 2.2.0
2025-11-15 10:12:46  Database version: 18.0 (Postgres.app)
2025-11-15 10:12:46  PostGIS version: 3.6
2025-11-15 10:12:46  WARNING: The pgsql (default) output is deprecated. For details see https://osm2pgsql.org/doc/faq.html#the-pgsql-output-is-deprecated-what-does-that-mean
2025-11-15 10:12:46  Initializing properties table '"public"."osm2pgsql_properties"'.
...
2025-11-15 10:12:50  All postprocessing on table 'planet_osm_roads' done in 0s.
2025-11-15 10:12:50  Storing properties to table '"public"."osm2pgsql_properties"'.
2025-11-15 10:12:50  osm2pgsql took 4s overall.
```

For this small a dataset, processing is done in seconds, even on my aging laptop. I truncated the output, but you should see no error messages here if the database is set up correctly. We can ignore the warning about the `pgsql` output for now, this will be removed in version 3, but that will be a while.

Let's confirm the data is loaded. A visual tool like QGIS is great for a smoke test, it has built in support for loading PostGIS tables.

![QGIS showing loaded OSM data](https://images.vanexel.net/image-2.png)

Excellent. Everything works! You have OSM data you can work with. You can already start a vector tile server for example, just install [pg_tileserv](https://github.com/CrunchyData/pg_tileserv), point it to your database and you're off to the races. Just add your styles!

```bash
$ DATABASE_URL=postgresql://mvexel@localhost:5432/osm ./pg_tileserv
```

![Unstyled vector tile in the pg_tileserv web UI](https://images.vanexel.net/image-6.png)


It gets more interesting when we move to using o2p Flex mode, which was added in version 2.

## Flexing Our o2p Muscle

One of the most exciting newer features of o2p is [Flex mode](https://osm2pgsql.org/doc/manual.html#the-flex-output). o2p used to be, well, inflexible. It was optimized for one purpose: load data optimized for the OSM raster tile server. Flex mode adds the use of Lua scripts to define data transformations, turning o2p into a universal data loader for most any ETL task with raw OSM as input.

Flex mode takes two inputs: an OSM PBF data file and a Lua script that defines the data transformations. the Lua language itself is beyond the scope here, but for anyone with some experience with any other programming language, the basics should be easy to understand.

The Lua script needs to define at least two things: a table definition and at least one processing callback function. The table definition is the DDL; here, we define the table metadata and the fields. The processing callback function defines what happens when o2p encounters a feature that matches the function's signature.

Here is a minimal example. We define a table `benches` with just two fields: a boolean `has_backrest` and a geometry column. The callback looks at all nodes that have `amenity=bench` and adds those to the table. Any other nodes pass through. There are no callbacks for ways or relations.

Let's save this file as `loader.lua` and we can use this file like this:

```bash
osm2pgsql --create --database osm --output flex --style loader.lua bogota.osm.pbf
```

After a very short while we have a `benches` table with `has_backrest` as a boolean field.

![Red dots are benches with no backrest](https://images.vanexel.net/image-3.png)

I will show one more example that is slightly more involved to give a taste of the more advanced processing capabilities that Flex mode enables using Lua transformations. The script below defines a `shops` table with name, address and tags values. Because address tokens are split across multiple attributes in the raw OSM data, I use a helper function to concatenate the parts.

I introduce callbacks for ways and relations, because address tags can be attached to features like building footprints. To transform the geometries to points, I use the methods o2p provides for geometry objects (`as_point`, `as_polygon`, etc).

Finally, I capture the "raw" OSM tags in a JSONB field.

This is by no means a production-ready script. I gloss over important details like defining proper indexes, dealing with the complexity of address tags in OSM, and properly classifying the POI to abstract away some of OSMs arcane conventions.

Nevertheless, we have a good-enough looking dataset we can display.

![Shops displayed in QGIS](https://images.vanexel.net/image-4.png)


## Easy enough, right?

I hope I was able to demonstrate that building a system for loading and filtering OSM data into a PostGIS database is not that hard for someone with a technical background. OSM and the Open Source Geospatial software ecosystem provides excellent tools that are used in high scale production systems. Today, software and data engineers without any geospatial background can implement entire production geospatial stacks. Where does that leave the geospatial domain experts like me?

I believe geospatial domain experts are in fact in a better position than ever to add value. Still, almost 20 years after the introduction of the iPhone and more than 25 years after the general availability of GPS, the need to add geospatial capabilities to applications is still growing and diversifying. The amount of open data has increased enormously, but the quality is all over the place and is often hard to assess. Complex relationships between disparate datasets require thoughtful data pipeline and storage design. New geospatial standards, like GeoParquet and STAC, are gaining rapid adoption and are evolving. Buy versus build decisions are tricky to navigate: Geospatial cloud offerings are plentiful now, but what are the trade-offs?

So, I think I will be good for a while 😄. I like to build systems that are built around the best that open geodata and open source software have to offer, with the minimum amount of complexity needed to get the job done. If you have an idea or challenge you would like to explore, [schedule some time to talk](https://cal.com/mvexel/30min)!
