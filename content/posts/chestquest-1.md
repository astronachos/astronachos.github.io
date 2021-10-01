---
title: "Chest Quest — Record #1"
date: 2023-05-05
tags: [chestquest, defold]
summary: "For the past 5 years I've been collecting bits and pieces of game mechanics, stories, and atmospheres that resonate in my heart. I think every developer has these chests of idea..."
cover:
  image: "/images/prototypes.jpg"
  hidden: true
---

## Collection

For the past 5 years I've been collecting bits and pieces of game mechanics, stories, and atmospheres that resonate in my heart. All of them certainly express hope for implementation. I think every developer has these chests of idea.

Among these notes may come across the phrase "It wasn't bad to create the atmosphere of a luxury hotel from the 80's." Or a whole article describing the gameplay, art style and scenario of the comedy game with the destruction of the fourth wall.

![Screenshots of prototypes and game jam entities](/images/prototypes.jpg)

Some of the ideas were borrowed from DOS games of my childhood, some are impressions from nowaday releases, and some are independently made up. Some things have been implemented as prototypes ([Love Is In The Air](https://astrochili.itch.io/love-is-in-the-air), [Cat's Day](https://astronachos.com/catsday), [Shipwrek!](https://astrochili.itch.io/shipwreck)), some are abandoned on prototyping phase. But so far there have been no releases. Unless you count [Rigibi](https://astrochili.github.io/rigibi/) in 2015, which is hard to call a game.

## Choice

And so, the determination to shake out the notes and finally do something. Involved, meaningful, up to and including release. What I like, what others might like, what's even realistic to make in a reasonable amount of time.

Throwing out old uninspiring ideas, I sorted the notes by the importance of answering the questions:

- How much code would it need to design?
- How much content would it need to create?
- How congruent is an inexpensive graphic style here?
- Is it possible to make comfortable controls on all platforms?
- How crowded is the market on the platforms?
- How successful are the analogs, if any?
- How excited am I myself about the idea?

By all accounts, a little session game mechanic that I picked up while surfing games with itch.io came out on top. If someone had told me five years ago that I would like to make a mini-game in the match3 genre, I wouldn't have believed it. Because I'm not a fan of that kind of game. But here is a special case, something hooked me in the implementation I came across.

## Core

The game mechanic is simple. A player starts with a set of items in his hand. Items have different values and colors. The player places them on the game board, but only items that have the same value or color can be placed next to each other. Forming chains of valid pairs in a line, the items disappear from the board, and the player earns points. If the items in the hand run out, new ones are given. When no valid moves remain, the game is over.

![Notepad with the first notes about the game structure](/images/notepad.jpg)

Instead of levels, there is only the progressive difficulty of the session. As the player progresses, new colors and values, obstacles, and useful bonuses are added to the item deck. When you lose, there is a delicate suggestion to clear the board or hand out other items. And, of course, a tiny store of useful items that can be applied during the game.

## Art

Normally, in match-3 games, items have a rigid link of meaning and color, such as a yellow sun cube, a blue water cube, etc. But with the current mechanics, items have two independent attributes, which makes it a bit difficult to represent them visually. I need items that are readable enough for the player to quickly navigate the board, and beautiful enough to have something to animate and to attach particle fx systems to.

For example, these can be RPG items belonging to different nature elements, or mysterious colored alchemical symbols. The original game, where I found the mechanic, uses game dices as items.

To be honest, I haven't decided on a graphic style yet. But I know it's worth adding a quick reskin feature, just in case understanding the graphic style comes too late.

Alternatively, these could be RPG items belonging to different elements, or mysterious colored alchemical symbols. The game, where I spied the mechanics, uses game dice as items. To be honest, I haven't decided on a graphical style yet. But I do know that it's worth implementing a quick reskin, just in case understanding the graphic style comes too late.

There are no fatal battles in this game, no managing of a big city, so it seems like there's a very static picture on the screen most of the time. This is one of the challenges - to make the view of the static game model as lively as possible, to deliver the most enjoyable UI / UX experience. To make even simple actions like dragging an item and placing it on the game board enjoyable.

How about the parallax effect on the background using the accelerometer data? The rare glitter of coins and the bubbling of potions? Sparks of fire from the placement of a orange magic sphere? Sounds of iron when placing an armor item?

![One of the old art experiments](/images/art.jpg)

## Code

I've found that for a turn-based game, it's very useful to first create a game module to interact with it through the console. This allows us to radically separate the logic of the game from the view. In addition to cleaner and better organized code, it allows you to cover the game logic with unit tests. It also allows you to run a special bot that is ready to play the game without interacting with the UI.

VSCode with the [Local Lua Debugger](https://marketplace.visualstudio.com/items?itemName=tomblind.local-lua-debugger-vscode) allows you to debug a core game module using breakpoints, independent of Defold. I like the lightweight experience of writing and testing lua modules using only the code editor. Before, I experienced something similar when I prototyped [Love Is In The Air](https://astrochili.itch.io/love-is-in-the-air) using the Love2D framework in 2016. Later on it was the experience of creating the [Narrator](https://github.com/astrochili/narrator) library, where unit tests and running a bot to find bugs came in handy.

There is another plus. You can reuse the game module in other game engines that use lua. For example, you may want to port the game to PlayDate.

## Time

This is the first my own project where I record time spent. Previously, when I made [apps](https://apps.apple.com/us/developer/roman-silin/id975493752), the estimates were very abstract. "It took about 300 hours, definitely not less," or "Well there were three weeks, so 4 hours a day, so that adds up to...". Not very clear, yeap? Now it's better.

![Time spent in April 2013](/images/timetable.jpg)

I put 50 hours into the game in April. Not so much for the month and not so little for the result. Because it's easy to get distracted and not notice you're wasting time on something that doesn't improve the game.

My inner perfectionist searched for the best way to implement lua table wrapping in a class, with support for automatic annotation of initialization parameters, for about 8 hours. But would this help the game? :)
