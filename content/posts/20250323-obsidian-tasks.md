---
title: Obsidian Tasks
date: 2025-03-23
tags: [productivity]
description: How (and why) I use Obsidian Tasks
---

I have been using Obsidian for note-taking for a while now. I love that its backend is just your file system with regular markdown files. I have only recently started looking into the rich community plugins ecosystem. One particular plugin I've particularly enjoyed is the [Tasks plugin](https://publish.obsidian.md/tasks/Introduction). I love you you can just use a Markdown checkbox and it will pop up an attributes dialog that lets you add due date, priority and so on. You can also access Tasks-related commands, like creating a new task at the cursor, using the Obsidian Command palette.

A very powerful feature of Tasks is [queries](https://publish.obsidian.md/tasks/Queries/About+Queries). You can use a triple-backtick code block with a `tasks` decorator anywhere to add a Task query block, looking something like this: 

````
## Due today
```tasks
due today
not done
```
````

I use this functionality to keep a dedicated Tasks note with various dynamic lists for tasks due today, later this week, soon, further into the future, as well as an Inbox of tasks that don't have a date attached to them. I thought I'd share the markdown for this page for people interested in using Tasks in this way:

````
### Inbox
```tasks
no due date
not done

```
### Due Today

#### Work
```tasks
due today
not done
tag includes work
```

#### Other
```tasks
due today
not done
tag does not include work
```
### Due later this week

#### Work
```tasks
not done
due after today
due in this week
tag includes work
```
#### Other
```tasks
not done
due after today
due in this week
tag does not include work
```
### Soon
```tasks
not done
due after 1 week
due on or before next month
```
### In the future
```tasks
not done
due after next week 
```

## To Done!

### Today
```tasks
done today
```
### This week
```tasks
done this week
```
````

And it looks like this:

![Obsidian Tasks page screenshot](https://images.rtijn.org/2025/obs-tasks.png)

### Postscript: Rationale

I have been a user of [Cultured Code's Things](https://culturedcode.com/things/) app for years. I love the thoughtful design and cross-device synchronization. The drawback is that it is another vendor lock in: Things is only available on Mac and iPhone / iPad. This locks me in to the Apple ecosystem just that tiny bit more. Obsidian is just Markdown files and therefore extremely portable.