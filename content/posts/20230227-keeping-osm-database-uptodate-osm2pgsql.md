---
title: "Keeping a thematic OpenStreetMap database up-to-date with osm2pgsql"
tags: [openstreetmap, gis, database, postgresql, geo]
date: 2023-02-27
---

Recently, I wrote and published [Resto-Bot](https://git.sr.ht/~mvexel/restobot), a Mastodon bot that toots about a restaurant POI from OpenStreetMap that has not been updated for more that 5 years. It is backed by an OSM database populated by the `osm2pgsql` tool, and I learned quite a lot about modern usage of this tool along the way. This post contains some of my learnings, and I hope it is useful for folks who would like to have a special-purpose OSM database that they can keep up-to-date.

![restobot post on mastodon](https://images.rtijn.org/2023/restobot.png)

## Modern `osm2pgsql`

`osm2pgsql` is a tool that populates a PostGIS database with OSM data from any OSM data file. It can also keep an existing database up-to-date using OpenStreetMap's replication diffs. The `osm2pgsql` I know from many years ago was mainly aimed at raster tile database use cases. It is still used today to populate the production database that powers the map tiles you see on osm.org. I don't dabble in tile servers much, so I never gave `osm2pgsql` much consideration. But the tool has evolved a lot! The most interesting enhancement for folks like me who are interested in data analysis is the "flex mode" [introduced in version 1.3.0](https://osm2pgsql.org/news/2020/07/28/release-1.3.0.html). In this mode, you can use lua scripts to define custom tables and perform all kinds of filtering and pre-processing as the data is being read. There is a [cache of lua examples](https://github.com/openstreetmap/osm2pgsql/tree/master/flex-config) in `osm2pgsql`'s Github repository, and [this example](https://osm2pgsql.org/examples/road-length/) from the documentation is a good starting point.

## Step 1. Designing the data model

For Resto-bot, I needed a database with just restaurant points-of-interest for the United States. A restaurant in OSM is represented as either a node or a way with the `amenity=restaurant` tag. To keep the database queries simple, I would like to avoid having different geometry types, so for restaurant ways (most commonly buildings with restaurant tags, [example](https://www.openstreetmap.org/way/995618499)), I would like to use the centroids. I don't need all OSM tags. I want to keep the option to use other POI types open, so I will use `class` and `subclass` fields to hold the OSM key / values for easy querying. I borrowed that pattern from [this example](https://osm2pgsql.org/examples/poi-db/).

So these are the fields I will need:

- `osm_type` and `osm_id`, so I can link to the original OSM feature in the bot's toot.
- `name`, so I can include the name of the restaurant in the bot's toot.
- `class` and `subclass`, this would be `amenity` and `restaurant` respectively for a restaurant POI, but I include these fields to keep the option open to expand to other POI types later.
- `geom` to hold the geometry. I don't strictly need this for the toot itself, but I want to do some spatial joining with Census boundaries so I can toot the city and state as well. I won't go into details on that part in this post.
- `timestamp`, the date and time the OSM feature was last changed. This is critical for our use case, because it lets us calculate how "stale" the feature is.
- `version`, the OSM feature version number, not strictly needed but I figured I may want to use it in the future.

## Step 2. Writing the lua script

The `osm2pgsql` flex output uses lua scripts to transform the raw OSM data into basically whatever model you want. There's [excellent documentation](https://osm2pgsql.org/doc/manual.html#the-flex-output) about this on the `osm2pgsql` website, so I won't go into too much detail, but I will walk you through [my lua script](https://git.sr.ht/~mvexel/restobot/tree/main/item/osm2pgsql/poi.lua). (This is my first ever encounter with lua, so there's probably room for improvement; let me know!)

The first part defines the PostGIS table. How to do this is [described in detail](https://osm2pgsql.org/doc/manual.html#defining-a-table) in the manual:

```lua=
local pois = osm2pgsql.define_table({
    name = 'pois',
    ids = { type = 'any', type_column = 'osm_type', id_column = 'osm_id' },
    columns = {
        { column = 'name' },
        { column = 'class', not_null = true },
        { column = 'subclass', not_null = true },
        { column = 'geom', type = 'point', not_null = true, projection = 4326 },
        { column = 'version', type = 'smallint', not_null = true },
        { column = 'timestamp', sql_type = 'timestamp', not_null = true},
}})
```

Notes:

- I used [advanced table definition](https://osm2pgsql.org/doc/manual.html#advanced-table-definition) rather than the default `define_node` to have more control over the table schema.
- The default projection `osm2pgsql` uses is EPSG:3857 or "web mercator". This is a common projection for map visualization, but for general purpose analysis I prefer to store geometries in unprojected lon/lat (EPSG:4326).
- We enforce `not_null = true` for all columns except `name`, which may be empty and the OSM feature would still be valid.

Otherwise, you will recognize the columns from my data model above.

Then we have a helper function that converts the object' timestamp to a valid ISO8601 string. I [borrowed](https://github.com/openstreetmap/osm2pgsql/blob/ad541d2a536a63de162d6266a6670aa2172b2749/flex-config/attributes.lua#L52) this function from the lua examples in the `osm2pgsql` repo:

```lua=
function format_date(ts)
    return os.date('!%Y-%m-%dT%H:%M:%SZ', ts)
end
```

Next, a function to parse the OSM objects. This is shared logic between the way and node callbacks, so we extract it into a separate function, which returns the entire lua table to be inserted except for the geometry:

```lua=
function process_poi(object)
    local fields = {
        name = object.tags.name,
        version = object.version,
        timestamp = format_date(object.timestamp),
    }

    if object.tags.amenity then
        fields.class = 'amenity'
        fields.subclass = object.tags.amenity
    elseif object.tags.shop then
        fields.class = 'shop'
        fields.subclass = object.tags.shop
    else
        return {}
    end
    return fields
end
```

The function returns an empty table when the feature is not a `shop` or `amenity`. This gives us more features than we strictly need for Resto-bot, but if we wanted to make a bot for, say, gift shops, then we would already have the data.

Finally, the [processing callback](https://osm2pgsql.org/doc/manual.html#processing-callbacks) functions for `node` and `way` features:

```lua=
function osm2pgsql.process_node(object)
    record = process_poi(object)
    if record.class then
        record.geom = object:as_point()
        pois:insert(record)
    end
end

function osm2pgsql.process_way(object)
    if object.is_closed and object.tags.building then
        record = process_poi(object)
        if record.class then
            record.geom = object:as_polygon():centroid()
            pois:insert(record)
        end
    end
end
```

The `node` callback checks if the `record` that `process_poi()` returns has a `class` key, and if not, moves on. (There may be more efficient ways to test for an empty table.)

The `way` callback checks if the object is closed and tagged as a `building`. If yes, the same pattern is followed. For the geometry, we call the `centroid()` function to get the building centroid so we can keep the geometry table simple.

## Step 3. Initial database loading

### Download and pre-process OSM data

To process OSM data, we need an OSM data file 😊. I got a United States PBF file from Geofabrik. Two nice things about the Geofabrik mirror are:

1. They have extracts for many different states / provinces, countries and continents.
2. They provide daily diffs for each area, which `osm2pgsql-replicate` can use to keep the database up-to-date later.

The initial loading needs to happen with in `--slim` modem otherwise the database cannot be updated with `osm2pgsql` later. This lengthens the initial loading time significantly and increases the size of the database.

### Create the database

`osm2pgsql` will not create the database for us, so let's do that first. We create a dedicated PostgreSQL user `osm` who will own the database, and create the database using it. Then we load the PostGIS extension.

```bash=
$ createuser -d osm
$ createdb -O osm osm
$ psql -d osm -c 'create extension postgis'
```

Now we're ready to....

### Load the data

We use `osm2pgsql` to read the OSM PBF file and populate the `osm` database we just created with it:

```bash=
osm2pgsql --slim -d osm -x -O flex -S mapping/poi.lua -c us-poi.osm.pbf
```

The options explained:

- `--slim`: use "slim mode". This slows down the initial import but is required if we want to keep the database updated.
- `-d osm`: the database to use
- `-x`: read the OSM object's attributes (version, timestamp). `osm2pgsql` skips these by default for performance reasons, but we need them.
- `-O flex`: use flex mode
- `-s mapping/poi.lua`: the lua mapping file to use
- `-c us-poi.osm.pbf`: the input file to use

See [the manual](https://osm2pgsql.org/doc/manual.html#pgsql-output-command-line-options) for all options available, or call `osm2pgsql` without any parameters to get a brief overview of the available options.

The output will end with something a lot like:

```
2023-02-24 12:53:54  Analyzing table 'pois'...

2023-02-24 13:25:49  Done postprocessing on table 'planet_osm_ways' in 1927s (32m 7s)
2023-02-24 13:25:49  Done postprocessing on table 'planet_osm_rels' in 18s
2023-02-24 13:25:49  All postprocessing on table 'pois' done in 12s.
2023-02-24 13:25:49  osm2pgsql took 4330s (1h 12m 10s) overall.
```

As you can see from the last line, loading a United States extract took 1h 12m on my laptop (Apple M1 Pro).

The database is now ready. A sample query:

```bash=
psql -d osm -A -c "SELECT osm_type, osm_id, name, current_date - timestamp::date as age FROM pois WHERE timestamp < current_date - (365 * 5) ORDER BY timestamp LIMIT 10;"
```

Result:

| osm_type | osm_id    | name                   | class   | subclass   | age  |
| -------- | --------- | ---------------------- | ------- | ---------- | ---- |
| N        | 29352403  | Boulders               | amenity | restaurant | 5763 |
| N        | 35314106  | Buffett                | amenity | restaurant | 5609 |
| N        | 81591111  | Memphis Blues          | amenity | restaurant | 5609 |
| N        | 81591406  | Tokyo Diner            | amenity | restaurant | 5609 |
| N        | 81592113  | Italian Oven           | amenity | restaurant | 5609 |
| N        | 93187662  | Colebrook Twin-Kiss    | amenity | restaurant | 5598 |
| N        | 93205236  | Blue Bird Inn          | amenity | restaurant | 5598 |
| N        | 88575147  | The Pink Store         | amenity | restaurant | 5598 |
| N        | 98197574  | Party House Restaurant | amenity | restaurant | 5594 |
| N        | 207734833 | AP's Seafood Buffet    | amenity | restaurant | 5530 |

So there you have it: from this extract, downloaded on 2023-02-11, the `amenity=restaurant` that has not been updated the longest in the United States is [The Hilltop](https://www.openstreetmap.org/node/26145635). (The place does still [seem to exist](https://thehilltopdanville.com/) but could use some additional tags in OSM!)

## Step 4. Keeping the database up-to-date

It is not extremely complicated to keep the database up-to-date: `osm2pgsql` includes a dedicated tool to do this! It's called [`osm2pgsql-replication`](https://osm2pgsql.org/doc/manual.html#keeping-the-database-up-to-date-with-osm2pgsql-replication). It works in two stages. First you need to initialize the database to support the replication process, then run the replication periodically[^1].

### Initialization of the replication process

The first step is initializing the database for replication using the `init` command:

```bash=
osm2pgsql-replication init -d osm --osm-file us-latest.osm.pbf
```

`osm2pgsql-replication` uses the input OSM file to determine where to start with replication, and where to source it from. It reads metadata from the PBF file (`osmosis_replication_base_url`. `osmosis_replication_sequence_number` and `osmosis_replication_timestamp`, I think) to determine those things. The documentation states that extracts from Geofabrik and `openstreetmap.fr` are guaranteed to have this metadata. Those sources also supply the replication diffs for the specific regional extract.

If you don't have the original PBF file anymore, you can skip that option. In that case, `osm2pgsql-replication` will look at the newest object in the database. In that case, the tool will use the standard replication diffs from osm.org unless you specify a `--server`. You may end up with a replication process that does not honor the geographic region of your original extract. I have not tried this.

### The replication itself

With the database initialized for replication, the tool can be run again with the `update` command:

```bash=
osm2pgsql-replication update -d osm --  -x -O flex -S osm2pgsql/poi.lua
```

The options explained:

- `-d osm`: database to use
- `--`: everything after this will be interpreted as parameters passed to `osm2pgsql`, which is used behind the scenes. We need to pass these options to ensure that the database is updated using the same mapping as when we originally populated it.
- `-x -O flex -S mapping/poi.lua`: the relevant original options passed to `osm2pgsql`.

The tool will retrieve the replication diffs and apply them to the database. If your initial OSM data file is older and / or it is of a large area, the initial replication can take a long time. You should probably consider downloading a fresh OSM data file and starting with a new import if your file is more than a couple of weeks old. In my case, the original file was from 2023-02-11 and today is 2023-02-27. Bringing the database up to date took a total of 16 minutes in two replication passes. The last line of the output reads:

```
2023-02-27 14:38:00 [INFO]: Data imported until 2023-02-26 21:21:07+00:00. Backlog remaining: 1 day, 0:16:53.253408
```

The "backlog remaining" varies depending on the replication diffs your source offers (Geofabrik has daily diffs), but also on the maximum diff size the tool will process in one run, which defaults to 500MB if not overridden with `--max-diff-size`.

To confirm that it works, let's query the database to find the most recently updated POI:

```bash=
psql -d osm -A -c "SELECT osm_type, osm_id, name, class, subclass, timestamp FROM pois WHERE class = 'amenity' AND subclass = 'restaurant' ORDER BY timestamp DESC LIMIT 10;"
```

Result:

| osm_type | osm_id      | name                      | class   | subclass   | timestamp           |
| -------- | ----------- | ------------------------- | ------- | ---------- | ------------------- |
| N        | 10695691769 | The Pickled Palate        | amenity | restaurant | 2023-02-26 21:17:56 |
| N        | 7601836481  | The Burger Stand          | amenity | restaurant | 2023-02-26 21:06:17 |
| N        | 10695644100 | Açaí Jungle               | amenity | restaurant | 2023-02-26 21:04:32 |
| N        | 10695638227 | M Shanghai                | amenity | restaurant | 2023-02-26 20:58:41 |
| N        | 10695665361 | 421 Craft Bar and Kitchen | amenity | restaurant | 2023-02-26 20:56:20 |
| N        | 8590304572  | Pizza Factory             | amenity | restaurant | 2023-02-26 20:50:25 |
| W        | 218847998   | Mi Mexico                 | amenity | restaurant | 2023-02-26 20:50:25 |
| W        | 218848660   | Red Robin                 | amenity | restaurant | 2023-02-26 20:50:25 |
| W        | 284621095   | Sushi Family Express      | amenity | restaurant | 2023-02-26 20:45:08 |
| N        | 10695624470 | Pizza House               | amenity | restaurant | 2023-02-26 20:37:54 |

And there we have it! [The Pickled Palate](https://www.openstreetmap.org/node/10695691769) in Mount Pleasant, SC is now the newest restaurant in our database. Thanks `Bolling` for adding it 😊.

The `osm2pgsql` manual has [good information](https://osm2pgsql.org/doc/manual.html#keeping-the-database-up-to-date-with-osm2pgsql-replication) on automating the replication process on a Linux flavored server. I'm not going to go into that topic, because this blog post is already plenty long!

## Thanks

Great tools like `osm2pgsql` are created by volunteers in the OSM community and released as open source software. Thanks Jochen (also for your feedback on a draft of this post!), Sarah, Paul and everyone else who contributed to the tool itself and, just as important, the high quality documentation. Also thanks to [Thunderforest](https://thunderforest.com), [FOSSGIS](https://fossgis.de/) and [Geofabrik](https://geofabrik.de/) who support development work on `osm2pgsql`. Both Geofabrik and Thunderforest deliver great products and services built on OSM data; consider them if you need OSM based map.

[^1]: For this particular use case, where we don't need the absolute latest OSM data and we are only interested in a small subset of data, it is certainly easier to just download a new extract and do a fresh database load without `--slim` whenever you want to update. I was curious and wanted to go through the replication process!
