---
title: "RapiD Bookmarklets"
tags: [openstreetmap, development, geo]
date: 2023-01-21
---

**Update: The bookmarklets are updated for the [new 2.0](https://developers.facebook.com/blog/post/2023/04/04/rapid2/) version**

I've been testing [the latest alpha version](https://mapwith.ai/rapid-v2-alpha3) of the [RapiD editor](https://mapwith.ai). RapiD is a web-based editing environment for OpenStreetMap that incorporates special layers with map features from other sources you can easily copy over to OSM. Examples include Microsoft's building footprint data, missing roads generated with machine learning, and open data from government GIS sources.

Because RapiD is not included in the dropdown menu where you can select an editor on the OpenStreetMap website, I created [a few bookmarklets](https://vanexel.net/bookmarklets.html)[^1] for myself that I hope come in handy for mappers who want to have quick access to RapiD. They are self-contained and don't read any content from your browser other than the current URL. If you're not currently on openstreetmap.org or actively editing in the default OSM web editor iD, the bookmarklets will simply do nothing at all.

I tested these bookmarklets in Firefox and Chrome. You can find the source code [here](https://git.sr.ht/~mvexel/rapid-bookmarklets/tree). When a new version of RapiD comes out, I'll do my best to update the bookmarklets.

[^1]: The bookmarklets are on a separate page, because this blog's markdown parser has trouble with the `javascript:` links.
