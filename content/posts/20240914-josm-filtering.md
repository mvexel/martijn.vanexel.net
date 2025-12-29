---
title: "JOSM tip: filtering"
date: 2024-09-14
tags: [gis, mapping, openstreetmap, osm, josm, geo]
description:
---

The OSM map gets denser and denser as more people contribute to the map. In a typical urban or suburban area, you will find trees, houses and other buildings, transit stops, land use et cetera. All this data can make JOSM slow and it can be hard to find the features in the map you want to focus on.

![screenshot of josm with lots of data](https://images.rtijn.org/2024/josm-busy.png)

That is where filters come in. Some I frequently use are buildings and land use. To add a filter, first make asure the filters pane is visible (Windows -> Filter or `Option`-`Shift`-`f`). Then click the `+` symbol. A standard JOSM search dialog appears:

![screenshot of JOSM search dialog](https://images.rtijn.org/2024/josm-search.png)

You can apply the same search criteria as usual when you search for things in JOSM. Simple filters are easy. In my case, I just type `building` in the search box and I am done. Then, click `+` again and add more filters as desired. I created another simple one filtering `landuse`. Both filters now appear in the filter list: 

![screenshot of JOSM filter pane](https://images.rtijn.org/2024/josm-filter-dialog.png)

You will see three checkboxes by each filter. `E` means that the filter is enabled, and is checked by default when you create the filter. `H` completely hides the filtered objects instead of showing them as a grey outline. `I` "inverts" the filter, so if you click the buildings filter, it will instead filter everything _but_ buildings. This animation shows the effect:

![animated GIF showing the effect of selecting and unselecting the various checkboxes by each filter](https://images.rtijn.org/2024/josm-filters.gif)

Happy Mapping!