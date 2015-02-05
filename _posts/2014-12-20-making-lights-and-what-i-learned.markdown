---
layout: post
title:  "Making Lights and What I Learned"
date:   2014-12-20 19:40:46
categories: programming
---
![Lights!](http://lights.williamg.me/style/lights.png)

## About Lights
I just recently finished developing an adaptation of the classic "Lights Out"
game. For those of you who aren't familiar, the object of the game is to turn
on all the lights on the grid. Whenever you toggle a light, all 4 adjacent lights
would also be toggled. In my version of the game, Lights, the same rules and 
objective apply. However, some lights are "directed" meaning when you toggle them,
only the adjacent light(s) in certain direction(s) are toggled, which increases
the difficulty of the game. The game itself has some fairly interesting nuances
to it. Firstly, the order in which you toggle lights doesn't matter. Assuming
you start with a board configuration A, if you toggle light 1 and then light 2, this
is equivalent to toggling light 2 and then light 1. Also, somewhat obviously, 
tapping on the same light twice (or any even number of times) is the same as not
tapping it at all. Likewise, tapping a light an odd number of times is the same
as tapping it only once. What this mean is every solvable board can be solved by
tapping a subset of lights only once. Pretty nifty.

## Design and Structure
I have a really bad habit of over-engineering my code. I actually started this
project last May, but I didn't get very far. I started looking at the code again
about a week ago and was pretty overwhelmed. It consisted of about five
different classes that did somewhat obscure things in very obscure ways. I started
by pruning away almost all of the existing code, and began working in a single file.
Surprisingly, this made my code *more* readable than when it was split up between
classes. It was easy to follow the code which greatly aided debugging. I think that
having everything in one place will also make it easier to maintain (to a point).
I've always had a thing about making my code "look good" and to me, long files always
failed to achieve this. However, I'm starting to see the beauty of consolidating code
and replacing lots of little classes with lots of little functions.

As far as the design of the game, I'm actually pretty happy with it. One
aspect that I didn't like was the fact that I couldn't find a way to add multi-directional
arrows without cluttering the design. If you play it, you'll notice that arrows
are always either up/down *or* left/right. Never both. There's nothing difficult about
this from a programming standpoint, but I absolutely could not find a way to 
elegantly add it to the design. I'm not too upset about this, but I never really
like when form gets in the way of function. Perhaps a better designer than I could
come up with a solution.

## Development
The development process went fairly quickly, which is a first. I did get to play
with a few things that I hadn't before, though. For example, this was the first
time I had ever used CSS3 transitions, and boy are they cool! It made the Javascript
so much simpler to have all of the animations handled by CSS. I know I'm barely
scratching the surface but I can see now why people have been so excited about
them.

I also got to work with cookies to keep track of users' best scores. The way Javascript
handles cookies is very interesting for me. Cookies are stored in `document.cookie`
as a "string." I put string in quotes because there's some sort of voodoo going
on behind the scenes. Namely, when you define a cookie, it's generally of the form
`name=value; expires <UTC DATE>`. You then assign this value to `document.cookie`.
But what if you want multiple cookies? Well, you do the same thing. But you only
specify one at a time. So, as an example, the following snippet would store three cookies:

{% highlight javascript %}
document.cookie = "name=John Doe; expires Sat, Dec 20 2014 12:00:00 UTC";
document.cookie = "age=24; expires Sat, Dec 20 2014 12:00:00 UTC";
document.cookie = "gender=Male; expires Sat, Dec 20 2014 12:00:00 UTC";
{% endhighlight %}

What gets to me is that it *looks* like we're assigning to `document.cookie` each time, but it never
gets overwritten. Furthermore, if we were to then read `document.cookie`, we would see

{% highlight javascript %}
"name=John Doe; age=24; gender=Male;"
{% endhighlight %}

What happened to the expiration dates?! Your guess is as good as mine. What made
cookies really annoying is that there doesn't appear to be a way to test cookies
using the `file:///` protocol, which makes local development very difficult. (As
an aside, Google Chrome supposedly has a flag to enable file cookies, but I couldn't
get it to work). 

## Takeaways
I think the biggest thing I gained from Lights was finally finishing a project
and releasing it to the public. I don't think I've ever done it before and it's
a really good feeling to finish something and be satisfied with it. I would deeply
appreciate any feedback you may have, you can contact me on Github or via email.
At the moment, my best score is 31. [Can you beat me?](http://lights.williamg.me)
