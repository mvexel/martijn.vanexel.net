---
title: I built a little command line tool to track my time
description: An in the process, I made some progress calming my brain
date: 2025-08-19
tags: [programming, javascript, node, productivity, cli]
image: https://images.rtijn.org/blog/20250820-timetracker/image3.png
card: summary # or summary_large_image
draft: false
toc: false
aliases:
---
**Non-geospatial post alert!**

As much as I love maps, I do like to take a break from them from time to time to scratch a non-geo itch. I like little side projects, because they give me an opportunity to learn something new, and find out how well my engineering skills, such as they are, translate to a domain I am not quite so comfortable with.

One side project that has been on my list for a while is a simple time tracker. I am doing paid consulting work from time to time (while still looking for a full time position, ahem) so it is important to keep track of what I spend my time on. It is also valuable for me personally to keep track of where my time goes. I am always in a fight with my ADHD brain about applying some structure to my work, resulting in me spending little chunks of time on things throughout the day on different projects. This makes it hard for me to close out the day (week, month) with a sense of accomplishment, because it feels like I started a lot of things but finished few.

I have tried a lot of different tools to keep track of my work: to-do apps, pomodoro timers, pencil and paper. To-do apps just overwhelm me—I keep adding things as they come up, and I try to organize and keep track, but it ends up becoming its own source of anxiety. Timers work completely counterproductively, they make me feel like I must spend the next set amount of time on something, but my brain won’t let me. Pencil and paper have been the most helpful to me throughout the years. A piece of paper does not impose any constraints on how I encode information. I can convey not just the facts but also a lot of mental metadata, adding emphasis, symbols, connections and stray thoughts, even tweaking my handwriting to add meaning. Even so, my notebooks are almost a write-only medium. I use one page per day, I’ll look at it throughout the day and then once more the next morning to carry over any tasks that I didn’t complete or summarize some thought, and then never look at it again.

While helpful, my notebooks don’t give me the retrospective I need to be able to track time to bill clients, or to get any clarity about how I spend my time in general. So I need something else. A tool that does not impose additional cognitive load, meets me where I am and gives me just the information I need, not more and not less.

I spend a lot of time in my terminal, and I like interacting with my computer by keyboard, so it makes sense for me to think about a terminal-based solution first. Developing a tool for the command line is also not something I had really done before—if you don’t count bespoke python scripts for data analysis and processing—so there’s a learning opportunity.

After a day and a half of iterating, brushing up on modern Javascript and figuring out zsh prompts and tab completion, I have what I think is exactly what I need. It’s called tt and I am very happy with it!

![alt text](https://images.rtijn.org/blog/20250820-timetracker/image2.gif)

It has just the commands I need:

* `tt start` to start a live tracking session. I have one running right now that I started with `tt start portfolio "write about timetracker-cli"`. portfolio is the project name, the quoted text is an optional comment. When I am in a live session, my prompt reflects this so I remember to stop the session when I move on to something else.

![alt text](https://images.rtijn.org/blog/20250820-timetracker/image1.png)

* `tt stop` to stop a live session  
* `tt log` to manually log a session, like `tt log portfolio 30 "did some more work"` to add time to the current day for the portfolio project. There’s an optional `--day` parameter to add time to another day  
* `tt logs` to see the entire log as a formatted table, or for a `--day` , `--week` or `--month`.
* `tt projects` to see a list of projects and how much time I spent on them and across how many days and sessions.  
* `tt summary` to get a summary of just the projects and time logged, optionally just for a `--day`, `--week` or `--month`.

I find that preferences and requirements for productivity tools are highly personal, but you are welcome to give it a try; the [project is open source](https://github.com/mvexel/timetracker-cli) (MIT) and you can install it with `npm i -g @mvexel/timetracker-cli`.

After finishing this post, I consider the project done for now. I frequently fall into the trap of thinking of more features and finding better ways to engineer parts of a project. That’s one more thing `tt` is already helping me with: telling me that I spent a grand total of 8 hours and 40 minutes on this project. Time to move on!

![alt text](https://images.rtijn.org/blog/20250820-timetracker/image3.png)

## Postscript: On Claude

I have not been living under a rock. Initially skeptical, I have become an enthusiastic user of Claude Code. I think it is an amazing and extremely powerful tool. I have used it in this project for two purposes: learning and writing tests. Those occupy opposite ends of the interestingness spectrum Learning is the most beautiful thing in the world and I would do it all day if I could. I ask Claude questions all the time, about what I am doing, how I could do it better, to discuss architecture (yes even a simple project like this has one!) and to help me learn modern Javscript, which I don’t know very well but is useful to know these days. Writing tests on the other hand is the absolute most boring thing that has ever been invented. Coding assistants do it very well, giving me more time to do work on better software.
