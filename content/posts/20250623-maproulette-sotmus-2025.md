---
title: MapRoulette at 13 years old
description: My presentation at State of the Map U.S. 2025
date: 2025-06-23
tags: [osm, sotmus, presentation]
image:
card: summary # or summary_large_image
draft: false
toc: false # or false or integer for max heading depth
aliases:
---

![Slide 1](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-01.png)

I have presented on MapRoulette quite a few times over the years. I started the project 13 years ago. MapRoulette has evolved a lot in all those years, but so has OSM, and so has the geospatial domain, and so has technology and society as a whole. Today, I would like to take stock and take the opportunity to look ahead and explore how MapRoulette may adapt to meet existing and new challenges in the years to come.

![Slide 2](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-02.png)

First, I want to go back and explore why MapRoulette came into existence, and why it still exists today. Next, I want to take stock. What have we accomplished, and perhaps more importantly, what have we not. That paves the way for looking ahead. What must MapRoulette do to stay relevant and meet the challenges OSM faces today? What should we change, and what should we keep?

![Slide 3](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-03.png)

Some of you may not have used MapRoulette before, or perhaps don’t even know what MapRoulette is. So let me walk you through the core MapRoulette workflow.

![Slide 4](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-04.png)

MapRoulette is a web application and API to provide the OpenStreetMap community with small tasks that meaningfully improve the map. You can check it out at maproulette.org. Mappers can choose between thousands of so-called Challenges, each covering a unique topic for a specific area. Each challenge has dozens, hundreds, even thousands of tasks, each at a specific, unique location, asking the mapper to solve a specific problem. What you see on this slide is the discovery page. This is where you can narrow down the challenges to find one that is of interest to you. You can use a variety of filters, free text search and the map to find your first task.

![Slide 5](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-05.png)

Here is one example. This task identifies a road, somewhere in the world, that has an unusually sharp angle. The page offers instructions, a zoomed out map that shows where the task is, and completion buttons that let you mark the task after you are done.

![Slide 6](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-06.png)

When you start the task, you are redirected to the OSM editor of your choice where you can fix the map as instructed.

![Slide 7](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-07.png)

After you committed your edits to OSM, you return to MapRoulette to embark on your next task! Tasks are assigned randomly within the same challenge to keep things interesting and expose you to different areas in the map, but you can also choose to have MapRoulette assign you a task that is close to the previous one.

![Slide 8](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-08.png)

To help me explain how and why MapRoulette came into existence, I pulled up this diagram made in 2009. OSM as a whole had only 117,000 registered users back then. But more than 70% of those had never made one single edit. Think about that: someone found out about OSM and was interested enough to go to the osm.org website, figure out how to create an account, filled out the form, and clicked on the confirmation email. And then…nothing! 

Of the folks who did go on to actually improve the map, only 44% only ever made one single edit, and never came back. So we’re down to about 12.5% of everyone who signed up who continued to edit the map after the first edit. 

This means that OSM was missing out on a lot of eyes on the map. People who were interested in OSM but, for reasons we can’t know, decided they didn’t want to contribute after all.

I saw this diagram at a presentation at State of the Map in, I think it was 2009 and started to think about it, and talk about it with other OSMers, a lot. What could we do to get better at converting non-mappers to mappers, and to encourage those who got started to continue mapping?


![Slide 9](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-09.png)

This was on my mind after the license change happened and the map was littered with artifacts from the redaction process. If you were around in 2012, you may have repressed the memories so let me quickly refresh your memory (if you weren’t, pay attention at your peril)

We had a different data license before 2012, Creative Commons Attribution Share Alike. After more than a year of arguing and lawyers, OSM decided to create a whole new license to fit our perceived needs, which is the Open Database License we still use today. Because of how Creative commons is framed, we had to go and ask every single contributor for their OK to adopt the new license for their specific contributions. A bunch of people had since disappeared or just didn’t care to respond, so we had to delete all their data. Only around 1% the OSM data had to be removed, but because most contributions build on one another in some way, we were left with a bunch of very strange artifacts in the data as pictured here, so it felt like much much worse. 

I had moved to the United Stated at that point and was working for a company, Telenav, where my job was to figure out how we could ensure OSM data would be good enough to power a mobile navigation app, and eventually the in-car navigation systems that were Telenav’s bread and butter. The quality of the map post license change was particularly problematic because the road network was particularly hard hit, as you can see in these screenshots.

![Slide 10](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-10.png)

The U.S. mapping community was very small at the time, with only around 100 active mappers daily. There were some useful tools available to help with what we called the “remapping” effort. One of them was a routing matrix depicted here. It compared current distance between US cities to a known reference distance and would color the matrix cells based on the difference between that reference and the current situation in OSM. This and other tools were useful but it didn’t tell casual mappers (most of OSM, then AND now) where to look to go fix OSM, and did nothing to bring new mappers into the fold to fix the map issues, many of which were pretty straightforward to fix.

![Slide 11](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-11.png)

To get more people involved to cover more ground and fix the map more quickly, we had to add a new ingredient. Something that would focus mappers on the specific issues caused by the license change redaction, and present it in a way that would make it easy for inexperienced mappers to engage.

I taught myself some basic web development skills as well as some python, and after some trial and error I was able to announce the ‘remap-a-tron’ a website that identified all the individual road network issues as individual tasks to solve. 

The remap-a-tron was crude but it worked really well, and as a community we were able to fix all the bugs in a matter of a few months.

![Slide 12](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-12.png)

13 years have passed since, and MapRoulette has undergone a lot of changes, let’s look at what we have achieved, and what we didn’t.

![Slide 13](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-13.png)

The community has solved more than 5 million tasks across more than 5000 different challenges. MapRoulette has been adopted by local OSM communities, but also by organizations and corporations to help with fixing and completing the map. MapRoulette is also used internally in organizations to organize the work of professional mapping teams.

![Slide 14](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-14.png)

We added many tools to help the community with creating challenges and tasks. There is an easy to use wizard for creating challenges, and each challenge has a comprehensive dashboard that shows progress, mapper leaderboards and the status of individual tasks.

![Slide 15](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-15.png)

Organized mapping teams benefit from advanced functionality such as peer-review dashboards and workflows. Challenges can be organized into projects which can be administered by multiple admins.

![Slide 16](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-16.png)

We introduced one-click ‘tag fix’ challenges that present proposed tag updates directly within the MapRoulette interface, where the mapper can directly accept them and they will be committed to OSM without having to open an editor.

![Slide 17](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-17.png)

We invested heavily in documentation and help.

![Slide 18](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-18.png)

MapRoulette is localized in more than 20 languages, all of which are contributed by the community through a crowdsourced translation website called Transifex.

![Slide 19](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-19.png)

Finally, we became one of the first OSM US Charter projects in 2023, providing a sustainable fiscal home for our project to facilitate donations and other contributions, and provided MapRoulette with the organizational backing of OSM US.

![Slide 20](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-20.png)

So MapRoulette has done a lot of thing, but there is an important thing we did not accomplish. To illustrate, we need to go back to those user attrition metrics we talked about earlier. 

You may have missed it but OSM reached 10 million users in 2023! Such a milestone is always good for some good vibes press. Even OSM marked this milestone in their special 20 birthday website! So, all those millions of people have surely been busy, right?

![Slide 21](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-21.png)

I ran the numbers recently and it turns out that an even smaller percentage of folks who sign up for an OSM account go on to make their first edit - only a little over 23%, down from 29% in 2009.

![Slide 22](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-22.png)

As in 2009, the number of mappers who make subsequent edits after their first edit then drops of again significantly.

![Slide 23](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-23.png)

It keeps dropping off as we look at mappers who make more than 10 edits (5.5%)

![Slide 24](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-24.png)

And finally, only about 120,000 mappers made more than 100 edits in their ‘mapping careers’.

So, MapRoulette has not been able to make a dent in the ‘churn rate’ among OSM sign ups. (There are of course many other factors at play here. Future work may be to analyze mappers first ten or one hundred changesets and see how many engage with MapRoulette early on in their mapping career.)

![Slide 25](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-25.png)

If we look at it from the angle of monthly active mappers we see a similarly disappointing picture. Of everyone with an OSM account less than 1% edits in a given month. The number hovers around 50,000 active monthly mappers. 

![Slide 27](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-27.png)

So, after all these years, OSM still needs to do a better job getting folks who sign up to make their first edit.

![Slide 28](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-28.png)

And OSM needs to do a better job getting new mappers to stick around as well.

![Slide 29](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-29.png)

We have not been able to address these challenges. There is still a huge amount of untapped energy of folks who are interested in maps, or even just interested in what is going on around them, how they get around, where they can go for a coffee or a hike, where they can get medical assistance, water or shelter.

![Slide 30](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-30.png)

MapRoulette is already pretty good at helping mappers who are already in OSM find meaningful work to do. We can still do a much better job at onboarding folks new to OSM. And we’re probably not helping much at all to convert non-mappers to mappers. What must we do to get better?

![Slide 31](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-31.png)

I see two strands of future development. First, we need to make the maproulette.org website easier to understand for new mappers who have little or no experience with OSM. We need to improve the discovery process and get mappers from logging on to maproulette.org to doing their first task that aligns with their interests much more quickly. That means we have to work on the discovery process. We may want to add natural language queries, for example. There needs to be more built in guidance and onboarding help. The UX needs to be more focused on the the core workflow of Discover -> See -> Solve -> Repeat.

Second, we need to meet more folks where they are. MapRoulette is good at engaging folks already in the OSM fold. But there are so many communities outside OSM that use OSM maps and may not even be aware of it. We need to build out the interoperability of MapRoulette and encourage, work on, and support creating and consuming map bugs outside of MapRoulette.org. Rapid editor is a good example of interoperability, you can see MR tasks on the Rapid map. It’s a start, but it’s another tool that lives inside the OSM ecosystem. What if folks using their own websites and apps outside of OSM can easily report as well as fix issues on the map? What if big OSM data users could better integrate their own error detection systems to interface with MapRoulette? There are crucial links between OSM and the outside world that are not made today. MapRoulette needs to be a central store of map issue signals that can be tapped into from within OSM but also outside of it.

We are currently working on the specifications of these developments. It is early days. I would love your help think through this. MapRoulette can only stay relevant if the community has a deep involvement in its design and development.

![Slide 32](https://images.rtijn.org/2025/sotm2025/maproulette-sotm-us-2025-32.png)

Thank you. MapRoulette has great potential and I need you to help unlock it for the years ahead. OSM is at an inflection point. Open geospatial data as a movement  is being appropriated by new entities that use OSM without doing very much to support OSM in return, or even acknowledge OSM in the way the project requires and deserves. AI is seen as the future of information retrieval, and some believe that the ideals of OSM as an enduring human mapmaking effort are in jeopardy of becoming irrelevant. I think nothing could be further from the truth. But to stay relevant, OSM needs to bring more people into its fold as contributors and advocates. It needs more mind share. If MapRoulette can play just a small role in this, I would be immensely proud.

Thank you for being here, and thank you for all you do to make OSM a great place to be.