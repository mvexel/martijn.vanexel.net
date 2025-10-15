---
title: Yes, please use OpenStreetMap for places data
description: Dead places data is not as good as alive places data
date: 2025-10-13
tags: [openstreetmap, geospatial, data]
image: og_image.jpg
card: summary # or summary_large_image
draft: true
toc: true # or false or integer for max heading depth
aliases:
  - osm/places
---

"OSM is great but we would never use it for POIs"

This has been a refrain for years. The argumentation goes something like: it's not complete enough and the license says we can't cherry-pick. 

Allow me to retort.

- You're probably already skirting—or crossing—the line in the way you use OSM data as laid out in ODbL
- What you're using today is probably not any better, even if you think it is. 
- You could be helping OSM get better at POIs but you are not.

Eplain yourself, I hear you say.

Okay.

## ODbL restrictions are a red herring
If I had a dime for every billable hour spent by corporate lowyers figuring out how to (creatively) interpret the terms "Derivative Database" and "[Produced Work](https://osmfoundation.org/wiki/Licence/Community_Guidelines/Produced_Work_-_Guideline)", I could probably retire today. I've had innumerable conversations with exasperated business leaders quizzing me about ODbL. Many have turned away from OSM because of the lack of clarity and legal precedent, but OSM has become too good to ignore, and business has found their own interpretations of the license, and OSMF gives only scant guidance that more often than not only adds to the confusion.

A fun example comes from the OSMF website's [page about "Regional Cuts"](https://osmfoundation.org/wiki/Licence/Community_Guidelines/Regional_Cuts_-_Guideline), a term that is used throughout OSMF's guidance but is not defined or mentioned in the formal license:

> The Regional Cut is at least a country. [Allowing smaller areas is an ongoing matter of discussion.] We would like you to avoid cherry-picking, i.e only picking small areas where OpenStreetMap is particularly good.

So, it's at least a country, but perhaps also not, and please avoid cherry-picking. IANAL but this would make my legal skin crawl. So it should not come as a surprise that businesses have become real creative with ODbL.<sup>1</sup> I suppose I could designate Salt Lake City, where I live and have spent years contributing to OSM and nurturing a community of fantastic map nerds, a "regional cut", and get fantastic OSM data for the city, and use something else where I find it lacking? Who is going to tell me I am wrong to do so? Certainly not this guidance. Another trick that I know is being employed is storing OSM and proprietary data in separate databases and answering spatial questions querying both at runtime, so any derivative data only exists ephemerally on the client. No problem there? Who is to say?

So don't talk to me about ODbL stopping you from using OSM for Places data. It's a red herring and we both know it.

## OSM data is not good enough

> This is why Open Street Map (sic) isn’t sufficient to standardize place. OSM is an amazing project, focused on making the best maps, freely available. But their dissemination mechanism is the map itself; everything is done in service of building that final map view.

This is Dave Breunig, of Overture, [concludes](https://www.dbreunig.com/2024/07/31/towards-standardizing-place.html) that OSM has too many technical impediments to be a good source of place data. OSM data, he argues, is distributed in an inscrutible format, and the tagging folksonomy is "not ideal" for anyone actually using the data.

Not only are both of these perceived impediments mostly solved problems, and have been for some time, Dave also misses the mark with the overall argument of his article: that (geospatial) data can only be useful if it is open and accessible, and to be accessible, it needs to be "cloud native" and have stable identifiers (which Overture helpfully provides). Do these things matter? Absolutely they do, but the real test of usefulness of geospatial data is not whether it is stored as GeoParquet on AWS and you can query and process it with DuckDB or Athena. I think these are fantastic tools to have, and they are actually quite easy to apply to OSM data by virtue of initiatives like [LayerCake](https://openstreetmap.us/our-work/layercake/). But none of that matters if the data you're making so easily accessible **does not actually represent what exists on the ground**.



[LayerCake](https://openstreetmap.us/our-work/layercake/)

<sup>1</sup> OSMF **does** have a Licensing Working Group that has some really knowledgeable people who have been in the ODbL trenches. So please do reach out to them if you genuinely seek guidance. But they are not a replacement for independent counsel, and definitely not for legal precedent.

## The ROI for helping OSM get even better at Places can be huge

You can be a multiplier 
OSM US camera grant program 
compare pedestrian



"Hey Google, where can I get some good Mexican food around here?"

Google Maps will give you a great answer: hundreds of millions of people help Google keep their places data up to date, for free. All incentives are aligned: Google, as an advertising company, wants to guide you to places that have paid them for the privilige to appear higher in their search results. Consumers provide active feedback: reviews, stars, photos, answering simple questions about a place, as well as passive feedback: location data from their devices, their inferred preferences. Business know that being easy to find and well reviewed on Google will help them make more money, so they update their business data for free and even pay Google to be easier to find. 

Because of these aligned incentives, first mover advantage (Google was not the first to put out digital map products for end users, but they definitely were the first to make them easy to use) and their massive scale and experience in search technology, Google has achieved network effects around places search that help produce a places product that is extremely hard to beat. If you drive a car that has built-in navigation that is not Google, you will have experienced this first hand: search results are out of date, incomplete and often irrelevant to your query. 

So why do they not just use Google then if they are so much better? The simple answer is that they can't. That simple answer obfuscates a more complex reality of 