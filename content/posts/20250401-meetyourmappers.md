---
title: Meet Your Mappers - Discover OpenStreetMappers Around You!
description: Meet Your Mappers is a new tool I wrote that lets you find active OSM mappers around you. It is free for anyone to use.
date: 2025-04-01
tags: [openstreetmap]
image: https://images.rtijn.org/2025/mym.webp
card: summary # or summary_large_image
draft: false
toc: false # or false or integer for max heading depth
aliases:
  - osm/meetyourmappers
---

I organize a monthly Map Night for OSM enthusiasts in Salt Lake City. There is a small group of regulars, but I actively try to reach out to people who might be interested in joining. One thing I wish I had to do this more effectively is a list of active mappers in my area, but there is no easy way to get such a list. So I built a website that will create that for anyone who is interested in a list like that for their local area, or any area they are curious about. It is called *Meet Your Mappers* and you can try it out [here](https://mappers.osm.lol/)!

This is what it looks like with the results for a small area around Salt Lake City:

![A screenshot of the Meet Your Mappers tool, with the results for a polygon area around Salt Lake City shown](https://images.rtijn.org/2025/mym-screenshot.webp)

To use it, you have to log in with you OSM account. This is to avoid abuse; I want this to be a tool for OSM mappers first and foremost. Once you are logged in, you can use the buttons on the right to enable drawing a box or a polygon on the map. To draw a polygon, click and drag. To draw a polygon, click to add points to the polygon and double-click to finish drawing. Once you have a shape drawn on the map, the `Meet Your Mappers` button will be enabled. Clicking this button will tell the website to start generating the list. This process may take a few seconds, depending on the size of the area. Once it is finished, you will see a list of active  mappers in your area. "Active" means that they made at least one edit in the past year. 

Each row in the table looks like this:

![Screenshot of one table row showing OSM username, action emoji, number of edits, and dates of first and last edit](https://images.rtijn.org/2025/mym-row.webp)

The information contained in the table, from left to right, is:
1. The mapper's OSM username. Clicking on the name takes you to their user page on the OpenStreetMap website.
2. The 👋 symbol. Clicking on it will take you to OpenStreetMap as well, but directly to the page where you can send the user a message.
3. the 📊 symbol. Clicking on it will take you to OSMCha, a powerful tool to inspect OSM edits. Here, you can click on each map edit (changeset) the mapper has made and find out exactly what they edited, and much more!
4. The number of map edits (changesets) the mapper has made in the past year.
5. The date they made their first edit. Again, this only applies to the past year.
6. The date of their most recent edit.

The table is fully sortable, you can click on any of the column headers to apply sorting on that column. When the table is first displayed, it is sorted by the number of changesets for that user.

For areas with a lot of mappers, Meet Your Mappers will only show the first 100 mappers with the most changes made. If you want to see more, you can click the `Export to CSV` button you will see when you scroll to the very bottom of the list. This will download a CSV file to your computer. You can load this into any spreadsheet program (LibreOffice, Excel, Google Sheets):

![A screenshot of a spreadsheet with the downloaded list of mappers](https://images.rtijn.org/2025/mym-csv.webp)

-----

The website source code is open source and MIT-licensed. You can find the code [on github](https://github.com/mvexel/osm-meet-your-mappers). If you find it useful, have suggestions for improvements, or just want to say hi, check [my website](https://ma.rtijn.org/) for contact information!