---
title: experiments with pyosmium, part 2
date: 2022-02-03
tags: [openstreetmap, geo]
---

*You are looking at the second of a 2-part blog post exploring how to extract useful information out of OSM data using python and PyOsmium. Check [Part 1](/20220126-pyosmium-part1) first and then come back! See you soon.*

At the end of Part 1, we had a very basic script that would read an OSM file and filter out the ways that was part of our list of Highways Of Interest, 

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

Cool cool, but not even close to what we were aiming for which, remember, is this:

![](https://i.imgur.com/QSx1IbU.png)

What we need to do to get to there is up next! I'm excited!

## On data structures...

We have two dimensions in our result data, the **known** list of highway values on the X axis and an for now **unknown** list of mappers who made one or more contributions to those particular OSM features. I would like to end up with a two-dimensional data structure that has the highway values (`motorway`, `trunk` et cetera) as column indices and the mappers' user names as row indices. This can be done with a simple 'list of lists' type structure, but I find those difficult to manipulate. [Pandas](https://pandas.pydata.org/), the data analysis package for Python, has a built-in data structure called a [DataFrame](https://pandas.pydata.org/docs/user_guide/dsintro.html#dataframe) that is specifically designed to hold and manipulate two-dimensional data, so we will use that. I am not very familiar with Pandas or the DataFrame structure, so I want to take a cautious approach where I instantiate a DataFrame with the correct dimensions. To be able to do this, I need to collect the list of mappers in a first pass, before collecting the metrics for highway mapping contributions. I'm really not sure if this two pass approach is really necessary, and I would love dive deeper in to Pandas and its data structures, but that will have to wait for another day!

## Collecting unique mappers

In the first pass of our script, we will build a list of unique mappers that contributed to any `highway` in our list. So we need to define a PyOsmium handler that will look at each way, check if it is a `highway` of interest, and add the mapper's username to a list if it is not already present:

```=python
class MapperCounterHandler(osmium.SimpleHandler):

    def __init__(self):
        osmium.SimpleHandler.__init__(self)
        self.mappers = []

    def way(self, w):
        if 'highway' in w.tags and any(elem in w.tags['highway'] for elem in MAIN_HIGHWAY_KEYS):
            if w.user not in self.mappers:
                self.mappers.append(w.user)
```

The most interesting line is probably `if 'highway' in w.tags and any(elem in w.tags['highway'] for elem in MAIN_HIGHWAY_KEYS)`, where we use `any()` in combination with a list comprehension to check if a way has a `highway` tag and if so, if the value is in our list `MAIN_HIGHWAY_TAGS`.

Let's embed this handler class in our script and change the `__main__` method to use it and emit the resulting list of mapper usernames:

```=python
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

class MapperCounterHandler(osmium.SimpleHandler):

    def __init__(self):
        osmium.SimpleHandler.__init__(self)
        self.mappers = []

    def way(self, w):
        if 'highway' in w.tags and any(elem in w.tags['highway'] for elem in MAIN_HIGHWAY_KEYS):
            if w.user not in self.mappers:
                self.mappers.append(w.user)

if __name__ == '__main__':
    my_handler = MapperCounterHandler()
    my_handler.apply_file('/home/mvexel/osm/data/test.osm.pbf')
    print(my_handler.mappers)
```

Running this will print a list of mappers. Pass 1 complete!

## Collecting Highway Mapping Counts

Now, we need to create a second PyOsmium handler to count mapping contributions for each mapper and `highway` type. We start by defining the DataFrame in the class's `__init__` method:

```=python
class HighwayCounterHandler(osmium.SimpleHandler):
    
    def __init__(self, mappers, all_versions):
        osmium.SimpleHandler.__init__(self)
        self.result = DataFrame(0, columns=MAIN_HIGHWAY_KEYS, index=mappers)
        self._all_versions = all_versions
        self._way_ids = []
```

We define the `DataFrame` with `MAIN_HIGHWAY_KEYS` as the columns and the list of mappers as the index. We will need to pass this list, which we built when we ran the `MapperCounterHandler`, in when we instantiate this handler.

Notice that we also create and assign a couple of other instance variables: `_all_versions` and `_way_ids`. The purpose of `_way_ids` is to be able to keep a list of way ids we have already encountered. This is relevant if we process a full history file, where all versions of OSM features are represented, not only the most recent version. When dealing with full history files, we want to give the user the choice to consider all versions of ways or just the latest. We will create a command line switch for this later. The `_all_versions` will hold `True` or `False` depending on that switch being present in the command line arguments. 

The actual counting is now fairly straightforward, because we use the same list comprehension to only consider relevant `highway`s:

```=python
    def way(self, w):
        if 'highway' in w.tags and w.tags['highway'] in MAIN_HIGHWAY_KEYS:
            if self.all_versions or w.id not in self._way_ids:
                self.result.at[w.user, w.tags['highway']] += 1
                self._way_ids.append(w.id)
```

## The Command Line

To complete this exercise, we need do a few more things to make the work we have done into a flexible command line tool. First, we need to add some logic to write out the result as a CSV file. Pandas DataFrames have a built in `to_csv()` method, which simply takes a path to write the CSV representation of the DataFrame to. Then, we need to wrap everthing in a command line interface. I like to use [click](https://click.palletsprojects.com/en/8.0.x/) for that purpose. I am not going to discuss how to define command line interfaces using click here, but with all the context from the previous sections, this part of the script should not be too hard to understand. 

The final result looks like this:

```=python
import osmium
import click
from pandas import DataFrame

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


class MapperCounterHandler(osmium.SimpleHandler):

    def __init__(self):
        osmium.SimpleHandler.__init__(self)
        self.mappers = []

    def way(self, w):
        if 'highway' in w.tags and any(elem in w.tags['highway'] for elem in MAIN_HIGHWAY_KEYS):
            if w.user not in self.mappers:
                self.mappers.append(w.user)


class HighwayCounterHandler(osmium.SimpleHandler):
    
    def __init__(self, mappers, all_versions):
        osmium.SimpleHandler.__init__(self)
        self.result = DataFrame(0, columns=MAIN_HIGHWAY_KEYS, index=mappers)
        self._all_versions = all_versions
        self._way_ids = []

    def way(self, w):
        if 'highway' in w.tags and w.tags['highway'] in MAIN_HIGHWAY_KEYS:
            if self._all_versions or w.id not in self._way_ids:
                self.result.at[w.user, w.tags['highway']] += 1
                self._way_ids.append(w.id)

@click.command()
@click.option('-a', '--all-versions', is_flag=True, help='Count all previous versions (if reading a full history file)')
@click.argument('osmfile', type=click.Path(exists=True))
@click.argument('output', type=click.Path())
def cli(osmfile, output, all_versions):
    click.echo("processing {}".format(osmfile))
    click.echo("Stage 1: Counting Unique Highway Mappers")
    mch = MapperCounterHandler()
    mch.apply_file(osmfile)
    click.echo("Done, {} mappers counted.".format(len(mch.mappers)))

    click.echo("Stage 2: Counting Highway Edits")
    hch = HighwayCounterHandler(mappers=mch.mappers, all_versions=all_versions)
    hch.apply_file(osmfile)
    hch.result.to_csv(output)
    click.echo("Done. Result written to {}".format(
        output
    ))

if __name__ ==  '__main__':
    cli()
```

Running this on an OSM full history file of Minnesota, it took around 45 seconds to generate a result:

```=bash
$> time count_highway_mappers --all-versions ~/osm/data/minnesota-internal.osh.pbf ~/tmp/minnesota-all.csv
processing /home/mvexel/osm/data/minnesota-internal.osh.pbf
Stage 1: Counting Unique Highway Mappers
Done, 2690 mappers counted.
Stage 2: Counting Highway Edits
Done. Result written to /home/mvexel/tmp/minnesota-all.csv

real    0m44.704s
user    1m7.943s
sys     0m3.954s
$>
```

I have not tested this with huge OSM files, and the script is not optimized for handling them. In particular, holding all visited OSM way ids in a list and using that list as a lookup table will probably cause memory and performance issues when operating on large OSM files. But as an exercise with some real world value, I think we succeeded! Find the full script with a README [here](https://github.com/mvexel/python-osmium-examples). Let me know if you enjoyed this and / or found it useful!