---
layout: post
title: "Fleet Devblog #1: Prototyping is hard"
date: 2017-01-05 13:00:00
categories: gamedev devblog fleet programming
---

As hinted in [my last post](http://williamg.me/blog/resolutions) I've recently
started work on a game. I intend to make this the first of a series of devblogs
chronicling my work on it. Today, I'm going to give a brief rundown of what the
game is (or how I imagine it) and the current state of the prototype I've been
building.

## Fleet

The working title for the game is Fleet; it's a bit of a mixture between chess
and Hearthstone with spaceships and RPG elements. Here's how it works:

- Before a match/out-of-game each player maintains his/her "fleet" which is a
collection of ships. Each ship has a pilot and some items. Pilots have some
stats, right now Precision, Accuracy, and Evasion, which control Crit Chance,
Hit Chance, and Dodge Chance, respectively. A ship's items are things like
weapons or shields or even signal jammers that can be used in game. Pilots' stats
or weapons can be *upgraded* out of game with some sort of currency that's earned
by winning/participating in games.
- Each ship additionally has a class which determines things like how many
items it can have, how much health it has, etc. For example, "Jumper" class
vessels can move around really quickly, but have low health and only one item
slot.
- In game, every action (moving, using an item) consumes a portion of the ship's
energy. A fixed amount of energy regenerates at the end of each turn.
- Once in-game, the object of the game is to destroy the opponent's mothership
before they destroy yours. The mothership will be at a fixed location opposite
your own mothership and can be damaged with weapons like any other ship.

Overall, I want the game to be simple-enough for anyone to jump in after a tutorial
and I also want it to be very open to casual play. However, I also think the
strategic elements make it a prime candidate for serious/competitive play.
I always planned on launching for desktop first, but had hoped I'd be able to
open it up to mobile as well (I'll be developing in Javascript so that's particularly
easy) but I'm starting to think that the amount of information necessary to
play will be hard to fit on a mobile screen. That's a ways off though.

## Prototyping

So here's the problem: I am *terrible* at prototyping. I'm the type of person that
enjoys the design and architecture part of software development probably more than
the actual implementation. I'd rather spend 10 days designing the perfect API rather
than designing 10 iterations every day. This is almost always a bad thing; iteration
is crucial. One article that I found particularly interesting against the idea
of premature-abstraction was
[Casey Muratori's post on "Compression Oriented Programming](https://mollyrocket.com/casey/stream_0019.html).
The idea of not abstracting until you need an abstraction, rather than abstracting
in anticipation of needing it, makes a lot of sense to me, even if it isn't
exactly what I've been taught.

So here's my progression from being very bad at prototyping to being slightly
less bad at prototyping. When I started working on
this game, the first thing I started working on was the server
networking architecture. And I spent at least an hour just *designing* it. This
was terribly unproductive for a few reasons:

- Networking is genuinely very hard to get right
- Networking is not as fun as the actual game
- Networking won't tell me whether or not the game is fun
- I can't get easily get feedback on the networking without also having developed
the game

A lot of these realizations came after having watched
[Extra Credit's excellent video on "MVP"](https://www.youtube.com/watch?v=UvCri1tqIxQ)
 I ended up scrapping a lot of what I had done and
started working on a purely client-side implementation. The games would be between
a human and an AI player so I wouldn't have to worry about anything server-side.
The next hiccup I encountered was the UI. The nature of the game pretty much
requires a fairly hefty UI to represent all the information you need and allow
you to take action appropriately. I originally had started out trying to do it all
on the canvas, trying to roll my own basic layout library before I caught myself
and searched for a better solution. I was working with web-tech anyway, so why
not just use HTML for the UI for now? Within about 20 minutes I had a fully
functional UI thanks to my existing HTML+CSS experience. That allowed me to
finally get to work on the important stuff: gameplay.

## Current Status

The gameplay is going well, and I have a few ideas for posts in the next couple
of weeks about interesting things I've done so far. Currently, the game supports
the following:

- 2 unique weapons
- Deploying vehicles from the hanger
- Moving vehicles on the board
- Using items
- Fully functional UI to do all of the above
- Turn-taking

An example of current gameplay is shown in the GIF below. The blue hexagons are
ally ships, and the red is an enemy ship. The green bars on the left side of hexes
represent health and the yellow represent energy.

![Fleet Prototype Gameplay][gameplay-gif]

Against my best wishes because the codebase is a mess, I've forced myself to
put all the code on [GitHub in its current state](https://github.com/williamg/fleet).
Try not to look at the client code because it's a mess. The game folder is a bit
better and more interesting.

## Issues & Next Steps
I have these ordered based on how important I think they are, so if you think
I'm wrong please let me know!

- Very basic AI; just enough to test more interactions
- Add "win" conditions so you can play a game to the end
- Allow users to customize their fleet (this actually might not be necessary in the prototype)
- Add support for status effects/items other than just weapons
- Improve AI to make it somewhat formidable

At this point, I'd say it would qualify as a "game". From there, I'd need to
add art, begin adding multiplayer, add much more content & items, etc. One step
at a time.

I hope you enjoyed my first devblog; hopefully there will be many more to come.
Until then, let me know what you thought on
Twitter ([@ganashaw](https://twitter.com/ganashaw)).See you next time!

[gameplay-gif]: /images/fleet-proto-gameplay.gif
