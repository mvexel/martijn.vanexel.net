---
title: "weekend hack: photo page"
tags: programming, hobbies
date: 2022-08-02
---

I love it when more than one of my hobbies come together in a small, contained project. This time it was biking, photography, urban exploration, and programming. I am not extremely good or talented at any of them, but doing and enjoying lots of different things and being okay at them is a good way to roll. You never know when things will intersect in interesting ways!

I like to get out on my bike or on foot and just explore the city. You see so much more using slow modes of transport than you do driving around. I am particularly interested in observing and documenting how places are changing. Over the years I have developed a bit of a sixth sense for places that will look very different a few years from now. Sometimes, imminent change is obvious: 

![obvious change](https://images.rtijn.org/2022/change-obvious.jpeg)

But often, not so much:

![not as obvious change](https://images.rtijn.org/2022/change-less-obvious.jpeg)

Anyway, I like exploring and documenting the not-so-remarkable places in my city. I created [an instagram account](https://www.instagram.com/alsoslc/) to post some of my photos, but I am not on instagram often, and I wanted a way to share photos in a way that I can better control. I wanted a page that has a map showing the locations of the photos I took on during bike ride, that let you click the photo locations to show the photo and perhaps some information about them.

Curiously enough, I did not find a great way to make a page like that. So I took it as an opportunity to hack something together. And a day later, I have something I am happy with! It's called [`geotagged-photos-to-webpage`](https://git.sr.ht/~mvexel/geotagged-photos-to-webpage). Naming things is not one of my hobbies. It is cobbled together using technologies I know just enough about: Python, HTML, a bit of Javascript, and Leaflet (a Javascript mapping library).

`geotagged-photos-to-webpage` takes a directory of photos in jpg format as input:

![directory with photos](https://images.rtijn.org/photopage-folder.png)

...and outputs an HTML page with a nice interactive map!

![webpage with nice map](https://images.rtijn.org/photopage-web.jpeg)

This is one of the hackier things I put together in recent times. The script basically works like this:

1. Iterate over a directory of jpg files
2. For each file, read EXIF location tags 
3. Generate a GeoJSON string from all photo locations
4. Inject this GeoJSON into a HTML template file
5. Save HTML and move images into place

The HTML template itself is just some plain old handwritten HTML and Javascript. I started with a basic Leaflet template, added some basic CSS, and some custom JS to connect map interactions with the rest of the page, and do some reverse geocoding. Good enough for me!

[Here's an example map page](https://images.rtijn.org/maps/20220729/). Happy with it!