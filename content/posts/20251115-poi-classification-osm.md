---
title: POI classification and OSM, a match made in hell
date: 2025-11-15
description: Teasing good POI data out of OSM is hard. I am doing it.
tags: [openstreetmap, gis, geo]
---

I am not the first poor soul to attempt this, nor will I be the last: mapping OpenStreetMap features to a sensible POI classification scheme. First off, the OSM free form tagging [folksonomy](https://en.wikipedia.org/wiki/Folksonomy) can be inscrutable, even for experienced OSM data users and contributors alike. One of the most active channels on the OpenStreetMap U.S. Slack is #tagging. The OSM Community Forum will have at least a dozen lively [discussions about tagging conventions](https://community.openstreetmap.org/c/help-and-support/tagging/71) going on at a time. It is a fantastic process to take part in or just observe; contributers care a lot about the things they see in the world that they want represented, from [pilgrimage route checkpoints](https://community.openstreetmap.org/t/checkpoint-on-a-pilgrimage-route/136725/4) to [filming locations](https://community.openstreetmap.org/t/does-a-tag-exist-for-tagging-a-filming-location/98596) and everything in between. Because of OSM's open data model, there is a place for almost everything on the map. It's also one of its main barriers for adoption. Data consumers like predicatbility, standards that are well defined and adhered to. Arguably the most important standard for OpenStreetMap would be a deterministic road classification. Yet, this is a topic within OSM that suffers from the [Eternal September Syndrome](https://en.wikipedia.org/wiki/Eternal_September), the same arguments being regurgitated over and over, to the extent that an exasperated OSM veteran [begs the community to never bring it up again](https://community.openstreetmap.org/t/lets-not-talk-about-highway-classification-in-osm/137150).

And that is just roads. Let's talk about Points of Interest (POI).

The number of distinct road classes you can come up with is probably no more than a dozen. A classification of POI can easily run in the hundreds of categories. Overture Maps maps [well over 2000 categories](https://github.com/OvertureMaps/schema/blob/main/docs/schema/concepts/by-theme/places/overture_categories.csv) to their internal taxonomy. (I do really want to check out a `fischbrotchen_restaurant` next time I am in whatever country has these.) You would at least expect OSM to have one or more tag keys that fully capture the POI feature layer. No such luck! There's `amenity`, `shop`, `tourism`, `leisure` and a few other top-level keys that harbor what you could consider POI.

Google [has](https://developers.google.com/actions-center/verticals/things-to-do/guides/partner-integration/location) a definition. LocationIQ [has](https://locationiq.com/glossary/points-of-interest) a definition. But really, I think it's [I Know It When I See It](https://en.wikipedia.org/wiki/I_know_it_when_I_see_it).

So if folks asks me if they should use POI from OSM, I tell them to sit down and strap in. It's going to be a ride!

It's a rainy Sunday here in Salt Lake City, both the Bills and the Packers won, so I find myself in a good enough mood to have fresh go at this. (Among my motivations is that I will need a solid category mapping for a mobile app I am working on that will make contributing to POI data quality in OSM easy and fun for anyone. More about that in a future post.)

## TagInfo

I need to know what we're working with. OpenStreetMap has intimidating [Map Features](https://wiki.openstreetmap.org/wiki/Map_features) documentation that contains both too much and not enough information at the same time. It will tell you about a very large but somewhat arbitrary subset of feature types defined and used by contribtuors. It will not tell you why some feature types are not listed, and it will not tell you how frequently each type is used, and its geographic distribution. For that, we have to look at the actual data, and a great way to do this is the [TagInfo](https://taginfo.openstreetmap.org/) tool. TagInfo lets you explore OSM data in a unique way: by tag key/value pair combinations. It will give you the actual live usage data of any tag combination you want to look at. I think it is the best way to start any exploration of OSM data.

![TagInfo interface](https://images.vanexel.net/image-9.png)

TagInfo has a very usable web interface that lets you search and drill down into the vast depths of OSM tagging. For my purpose today, I want to use it through its API, which is publicly accessible [and documented](https://taginfo.openstreetmap.org/taginfo/apidoc). My strategy here is:

1. Define a list of top-level keys that contain POI
2. Fetch the most frequently used values for those keys (generously, any value that represents more than 0.1% of the total)
3. For each of those k/v pairs, consider those that have at least 25% coverage for the `name` tag, or at least 10,000 individual features with `name`.

The simple rationale is that POI generally have names. I don't think it needs to be more complicated than that. Later, when we actually process real data, the features without `name` will get dropped anyway. I wrote a [simple script](https://gist.github.com/mvexel/6c43da11270f18bec61cf958d65ef4f5) I use to compile this tag list, and it contains 334 unique tags. This is what the first few lines look like.

```
key,value
aeroway,helipad
aeroway,aerodrome
aeroway,terminal
amenity,parking
amenity,place_of_worship
amenity,restaurant
amenity,school
amenity,fast_food
```

## The Labor of Mapping

I have my OSM POI tags, but I also need something to map them to. I want the classification to be small enough to be instantly understood by someone using a consumer map / POI search app. Who does POI search better than Google? Nobody. So let's use [their top level categories](https://developers.google.com/maps/documentation/places/web-service/place-types#table-a)! There's 19 of them: Automotive, Business, Culture, Education, Entertainment and Recreation, Facilities, Finance, Food and Drink, Geographical Areas, Government, Health and Wellness. Housing, Lodging, Natural Features, Places of Worship, Services, Shopping, Sports and Transportation.

I thought I'd have a laugh and ask Claude / ChatGPT to map the OSM tags to this list, and both did a hilariously bad job, confirming the inevitability of the next thing I need to do: manually going over all 334 OSM tags and mapping them to one of these 19 categories.

![Claude's attempt at mapping](https://images.vanexel.net/image-10.png)

I have a list that I am happy with, and it's still raining outside. [Here it is](https://gist.github.com/mvexel/2122956734bdb27b7ce7c707f4337711). You will notice that a few tags didn't make the cut. OSM's obsession with rail is to blame, I don't see any reason to include `railway=razed` in my POI database.

The hard work is done! Let's process some data. I have a small OSM data file sitting around from my [previous post](/etl-with-openstreetmap-simple-things-are-simple/), so let's re-use that. I wrote a Lua transform script to use the mapping defined in the CSV file to load Bogotá POIs into PostGIS. The script is [here](https://gist.github.com/mvexel/32905874ef2a5d3625263b6eb3efb8c2).

The data is small so this just takes a few seconds. And we have a POI map for Bogotá with names, contact information and category.

![POI map of Bogotá](https://images.vanexel.net/map.png)

This is a great start. Most of the work was the manual classification. You're welcome to use it in any way you like. However, turning this into something that can power my social check in app (that's for a future post), I need to do a lot more work. To get this data from 80% to the best it can be, there is a lot more work to do. The classic 80/20 curve applies: most of the remaining effort will go toward diminishing returns. We need to think about quality metrics, filtering out low confidence features, parsing opening hours, finding other datasets that may provide hints of what we may be missing, perhaps even a human map improvement project if the scale fits. Is that worth it? With POI, you can't really afford to not get it right. Pointing users to a [restaurant that has been closed for 6 years](https://plan.tomtom.com/en/location/id/qZAhi9ARfZuwYB2Q6FNl8Q?p=40.74956%2C-111.8649%2C18.94z&q=Mazza) is not going to delight your customers. Doing POI at scale is very, very hard. An upcoming post will go into some of the solutions that are starting to emerge.
