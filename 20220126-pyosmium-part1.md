---
title: experiments with pyosmium, part 1
date: 2022-01-26
tags: openstreetmap
---

*You are looking at the first of a 2-part blog post exploring how to extract useful information out of OSM data using python and PyOsmium. Check [Part 2](/20220203-pyosmium-part2) for the follow-up.*

Osmium is a very powerful library and command line tool for OSM software. Among its capabilities are data format conversion, filtering by tags, diffing OSM files, and applying changes. Osmium does those things and many other common ETL tasks very quickly. We have Jochen Topf to thank for osmium, with [contributions](https://github.com/osmcode/libosmium/graphs/contributors) from many others.

For folks like me who are not too comfortable writing C++ code, there is a wrapper around libosmium that exposes most of its functionality to Python, called [PyOsmium](https://osmcode.org/pyosmium/). I've been working with PyOsmium off and on for some years, but a recent thread on the OSM Slack made me want to dive back in and do this write-up.

## Highway Mappers

A number of active mappers in the U.S. community has taken on a [large effort](https://wiki.openstreetmap.org/wiki/United_States/2021_Highway_Classification_Guidance) to harmonize highway tagging for the United States. Initially covering a few East Coast and New England states, there are now initiatives in many states to adopt the new guidelines. AS far as I know, this is the first U.S.-wide initiative on this topic that prioritizes including as many mappers as possible. (In fact, I am hosting a virtual meetup tonight to discuss this topic for Utah.) Mappers who are most likely to want to contribute to the discussion are those who have actually done a lot of editing on the major highways.

This is where my exercise with PyOsmium comes in. Although it is probably possible to generate such a list using Overpass, I like the idea of a Python script: it can be easily adapted to different use cases, it is only limited by the memory and processing power on the machine you are run it on, and it's more fun to write :slightly_smiling_face:. 

The result of the work I describe in this post is [on GitHub]().

## PyOsmium Concepts

PyOsmium has good documentation that I don't want to duplicate here, but there are some core concepts that can help you understand how to follow along and write your own PyOsmium scripts.

### Handlers

PyOsmium operates on OSM data using a handler class. This class derives from `osmium.SimpleHandler`. Inside your handler class, you define `node`, `way`, and `relation` methods that operate on the respective OSM features passing through as the OSM data is read. A skeleton would look like this (omitting the `__init__` method):

```python=
class MyHandler(osmium.SimpleHandler):
    
    def node(self, n):
        # your node processing code goes here
        
    def way(self, w): 
        # your way processing code goes here
        
    def relation(self, w):
        # your relation processing code goes here
```

The OSM objects passed into these methods derive from [`osmium.osm.OSMObject`](https://docs.osmcode.org/pyosmium/latest/ref_osm.html#osmium.osm.OSMObject) and have properties such as `id`, `timestamp`, `changeset`; the metadata you would expect. There's also `tags` which is a [dictionary-like object](https://docs.osmcode.org/pyosmium/latest/ref_osm.html#osmium.osm.TagList). Each feature type adds its own relevant properties, such as `location` for a node, `nodes` for a way, and `members` for a relation.

to process an OSM data file, you instantiate your handler class and apply an OSM data file, like so:

```python=
my_handler = MyHandler()
my_handler.apply_file('path/to/data.osm.pbf')
```

`libosmium`, and by extension PyOsmium, can read all common OSM data file formats, including 'full history' files. See the [`libosmium` documentation](https://osmcode.org/file-formats-manual/) for information on file formats.

These are just the basic concepts. You should to study the [PyOsmium documentation](https://docs.osmcode.org/pyosmium/latest/) if you want to write your own software.

## Creating the Highway Mappers script

Let's look at how we can use our knowledge of PyOsmium and python to write a script that will tell us which mappers contributed to major highways. The result will look something like this:

![](https://i.imgur.com/QSx1IbU.png)

Since we're only interested in ways, we know that we don't need functions to handle nodes and relations. Let's start laying out our handler class:

```python=
class HighwayCounterHandler(osmium.SimpleHandler):
    
    def __init__(self, mappers, all_versions):
        osmium.SimpleHandler.__init__(self)

    def way(self, w):
        pass
```

Since know exactly which `highway` values we're interested in, let's keep those in a list:

```python=
MAIN_HIGHWAY_KEYS = [
    'motorway',
    'motorway_link',
    'trunk',
    'trunk_link',
    'primary',
    'primary_link',
    'secondary',
    'secondary_link', 
    'tertiary',
    'tertiary_link']
```

Next, let's dress up the `ways(self, w)` function so that any OSM object that passes through that is not a `highway` with a value in our list passes through:

```python=
    def way(self, w):
        if 'highway' in w.tags and w.tags['highway'] in MAIN_HIGHWAY_KEYS:
            print("way {}, highway type {}, mapper {}".format(
                w.id,
                w.tags['highway'],
                w.user))
```

We're using a list comprehension here to do the filtering in a pythonic way. 

Let's add a `__main__` function so that we can run this early version of the script:

```python=
if __name__ == '__main__':
    my_handler = HighwayCounterHandler()
    my_handler.apply_file('/home/mvexel/osm/data/test.osm.pbf')
```

Now al that is missing to test this first version of our script is an OSM data file. For this script to be useful, we will need a file that contains mapper information. You can get these at the [dedicated Geofabrik download site](https://osm-internal.download.geofabrik.de/). You will need to log in with your OSM credential to be able to download these enhanced OSM files, and you have to abide by the conditions placed upon using these files. I recommend picking a small file to start and test with.

The entire script so far looks like this:

```python=
import osmium

MAIN_HIGHWAY_KEYS = [
    'motorway',
    'motorway_link',
    'trunk',
    'trunk_link',
    'primary',
    'primary_link',
    'secondary',
    'secondary_link', 
    'tertiary',
    'tertiary_link']

class HighwayCounterHandler(osmium.SimpleHandler):
    
    def way(self, w):
        if 'highway' in w.tags and w.tags['highway'] in MAIN_HIGHWAY_KEYS:
            print("way {}, highway type {}, mapper {}".format(
                w.id,
                w.tags['highway'],
                w.user))


if __name__ == '__main__':
    my_handler = HighwayCounterHandler()
    my_handler.apply_file('/home/mvexel/osm/data/test.osm.pbf')
```

Save this in a new, empty directory as count_mappers.py

To run this, or any, python script, best practice is to create a virtual environment to isolate your python executable and script dependencies:

```bash=
python3 -m venv venv
source venv/bin/activate
pip install osmium
python count_mappers.py
```

The output should look something like this:

```
way 950219012, highway type primary_link, mapper A Hall
way 950219013, highway type primary, mapper ezekielf
way 951233358, highway type secondary, mapper ezekielf
way 951233359, highway type secondary, mapper ezekielf
way 952508497, highway type primary, mapper A Hall
way 955148828, highway type trunk_link, mapper Joseph R P
way 955174789, highway type trunk, mapper ezekielf
way 955174790, highway type tertiary, mapper Stan Brinkerhoff
way 958183113, highway type tertiary, mapper dchiles
way 958183114, highway type tertiary, mapper dchiles
way 958540604, highway type secondary, mapper jared
....
```

We now have a useful framework to work with, but we still have much work to do. We need to remember each mapper and how many edits they made for each highway type, and we need to output this information as a nice CSV file. In the [next installment]({% post_url 2022-02-03-experiments-with-pyosmium-part-2 %}), we will take care of those things and get to the final result.