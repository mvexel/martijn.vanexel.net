---
title: My Obsidian Daily Note Template
description: I went down a little Obsidian templater rabbit hole..
date: 2025-03-28
tags: [productivity]
image: https://images.rtijn.org/2025/obs-template.png
card: summary # or summary_large_image
draft: false
toc: false # or false or integer for max heading depth
---

I spent too much time today on tweaking my Obsidian daily note template. Here is the final thing, read below for a chunk-by-chunk explanation.

```
<% tp.user.motivational() %>

> [!todo]
> - [[Tasks#In the future]]
> - [[Tasks#Anytime]]
> ### ⚠️ Overdue
> <%* if ([0,6].includes(tp.date.now("d"))) { %>
> ```tasks
> due before today
> not done
> ```
> <%* } else { %>
> ```tasks
> due before today
> not done
> tag does not include work
> ```
> <%* } %>
> ### Inbox (Aim for zero!)
> ```tasks
> no due date
> not done
> no tags
> ```
> ### Due Today
> <%* if ([0,6].includes(tp.date.now("d"))) { %>
> ```tasks
> due today
> not done
> ```
> <%* } else { %>
> ```tasks
> due today
> not done
> tag does not include work
> ```
> <%* } %>

<% tp.file.cursor() %>
```

A fresh daily note page generated from this template looks like this:

![screenshot of blank daily notes page](https://images.rtijn.org/2025/obs-daily.png)

## Explanation

### Motivational Statement

A randomly selected motivational statement. This uses the [Obsidian Templater user scripts](https://silentvoid13.github.io/Templater/user-functions/script-user-functions.html) functionality. The script:

```javascript
function get_statement() {
  const motivationalStatements = [
      "💪 Every day is a new opportunity to shine. ✨",
      "☀️ Embrace the possibilities that lie ahead. 🚀",
      "👏 Believe in yourself and your abilities. 🌟",
      "👍 Your hard work will pay off in the end. 🌱",
      "😊 Stay positive, even when things get tough. 🌈",
      "😎 Make today amazing. 🔥",
      "😇 You are capable of achieving great things. 🏆",
      "💯 Strive for progress, not perfection. 💫",
      "🎯 Focus on your goals and don't give up. ⛰️",
      "💡 Let your creativity guide you. 🎨",
      "💖 Spread love and kindness wherever you go. 🕊️",
      "🌱 Grow through what you go through. 🌻",
      "🚀 Take the first step towards your dreams. 🌠",
      "🎈 Celebrate every small victory. 🎉",
      "🧘‍♀️ Find peace and balance within yourself. 🍀",
      "😃 Choose happiness today and every day. ☀️",
      "🤝 Together, we can make a difference. 🌍",
      "🎼 Fill your day with positive vibes and energy. ⚡",
      "📖 Write your own story. ✍️",
      "🌟 You are enough, you are worthy, you are loved. ❤️"
  ];
  const randomIndex = Math.floor(Math.random() * motivationalStatements.length);
  return motivationalStatements[randomIndex];
};

module.exports = get_statement;
```

### Tasks overview

This has a couple of backlinks to my Tasks page (which I [wrote about](20250323-obsidian-tasks) before) and expanded lists of the most relevant lists: what is overdue, what is not filed yet (in my inbox), and what is due today. This is rendered as a [callout](https://help.obsidian.md/callouts) so it stands out and I can collapse it. The `[!todo]` statement at the top is a special callout type that will render the callout with a `✓ Todo` title.

There Javascript conditionals ensure that I do not see any work-related tasks on weekends 🤓. This is supported through Templater's [Execution Commands](https://silentvoid13.github.io/Templater/commands/execution-command.html). 

### Cursor position

Finally I use the Templater plugin's [`cursor`](https://silentvoid13.github.io/Templater/internal-functions/internal-modules/file-module.html?highlight=cursor#tpfilecursororder-number) function to put the input focus below all this.
