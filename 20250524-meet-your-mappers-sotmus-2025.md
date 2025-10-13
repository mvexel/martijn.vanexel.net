---
title: Meet Your Mappers
description: My lightning talk at State of the Map U.S. 2025
date: 2025-06-24
tags: [osm, sotmus, presentation]
image: https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-01.png
card: summary # or summary_large_image
draft: false
toc: false # or false or integer for max heading depth
aliases:
---

These are the slides and cleaned up speaker notes for my lightning talk about the [Meet Your Mappers](https://mappers.osm.lol/) tool at State Of The Map U.S. in Boston.

![Slide 01](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-01.png)

Hello everyone! Excited to update you on a new tool I wrote, Meet Your Mappers!

![Slide 02](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-02.png)

I have been running OSM Utah for a while. We have a great group with some of the most active mappers in the Salt Lake City area. I am always looking for more active mappers to invite!

![Slide 03](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-03.png)

When I am looking for folks to invite, I ask myself these questions.

![Slide 04](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-04.png)

There are some tools that show you other mappers in your area. So why not use those?

![Slide 05](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-05.png)

First, there is the OSM.org dashboard on your profile page. You can follow other users, but this is honestly not very useful for my purposes. There is no way to search for mappers that have been active in my area. The users that show up on the map as pins are those who have set their home location at that location. This is optional, and there is no guarantee that they are mapping in the area.

![Slide 06](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-06.png)

Then there is the Neis ‘Who’s Around Me’ tool. This answers the questions a bit better but has some drawbacks that makes it hard for me to use for my purposes.
First, you can’t define your area of interest. The visible map area, which is always a rectangle, is where the tool searches for mappers. You can’t add a polygon to define a more specific area of interest. Second, I find the settings and options quite confusing to use. This is very personal, but I like tools that are simple to use for things I do infrequently. Third, there is no way to sort the results by number of changes, or when mappers first started editing. Lastly and most annoyingly, the tool is not open source so there is no way to do anything about it.

![Slide 07](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-07.png)

So I made something new: Meet Your Mappers!! You can use it right now at mappers.osm.lol.

![Slide 08](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-08.png)

It has some key features that make it useful for myself and hopefully for you too!

You can define your custom area as a bounding box or a polygon. So you can draw exactly around your city, neighborhood or any geographical area you like.

The resulting mapper list is sortable by name, number of changes and the date of their first and most recent change. Next to the name, two icons let you quickly open the osm.org new message page to send a message to the user, and view their contributions for the area you defined in more detail in OSMCha. You can also download the entire list as a CSV file to process any way you want, for example in a spreadsheet program.

The tool is always up to date, it keeps up with the live OSM database every minute. 

You can share the link to your specific Meet Your Mappers view easily with others. 

And finally, it is completely open source!

![Slide 09](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-09.png)

Here’s how it works. First, you log in using OSM. This will send you to osm.org to allow Meet Your Mappers to confirm that you are an OSM user.  Meet Your Mappers does not store anything about you and this does not create an account on my system.

![Slide 10](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-10.png)

Then, you define a polygon or bounding box by drawing on the map. For a bounding box, just click and drag. For a polygon, draw by clicking each point and double-click to finalize the shape.

![Slide 11](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-11.png)

Then, simply click “Meet My Mappers” and we will get to work!

![Slide 12](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-12.png)

Once the list is generated on the backend, it will be displayed below the map. For each mapper, you will see the icon links  mentioned earlier, the number of changesets they created, and the date and time of their oldest and most recent edits. You can simply click on the column headers to sort by that column.

![Slide 13](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-13.png)

When you export the CSV file, you can import it in any spreadsheet program or process it with a custom script.

![Slide 14](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-14.png)

There are a few limitations to Meet Your Mappers.

First, you cannot draw huge areas. This is because Meet Your Mappers queries a live database, and the tool runs on a modest server. A neighborhood or city will work, a state or country will not. I think this is a reasonable limitation.

Second, the tool only contains data one year back. This is a deliberate decision. It helps keep the application reasonably fast, and we’re only looking for active mappers anyway, so mappers who did not do any mapping in the past year are irrelevant for our purposes..

Last, the application is designed for use on a computer. It is barely usable on mobile. I don’t think mobile is an incredibly important use case, but feel free to contribute if you think it is!

I think the tool does not really need that much else. I am always open to suggestions. One thing that may be nice to do is to host it on OSM US infrastructure, which would save me around $20 a month in Hetzner VPS costs. I think this tool would be a good candidate for an OSM US Community Project.

![Slide 15](https://images.rtijn.org/2025/sotm2025/meet-your-mappers-sotmus-2025-15.png)

Thanks for being here!
