---
layout: post
title:  "My First Foray In Open Source Software"
date:   2015-1-26 20:13:00
categories: programming
---

## Prelude

Almost all programmers that I've talked to are huge proponents of open source
software. Recruiters avidly encourage job applicants to contribute to open source
software because they love to see it on resumes. Until a few days ago, I had never
contributed to any open source project other than my own. Finding myself terribly
bored one weekend, I decided to change that. So this is a brief summary of my first
contribution to open source software.

## Step 1: Find a Project

This has almost always been the hardest part for me. Ideally, you want a project
that doesn't have a huge codebase already. Less code makes it easier for you to
contribute right away. However, you also want your contribution to matter. The
incentive to contribute to an obscure command line utility that only works on
Ubuntu 10.02 is minimal at best. I used Github to explore interesting projects.
There are also websites that pick out Github repos with the most "issues" but
the problem with that is that most of these projects have literally 1000s of issues.
That can be pretty intimidating for a newcomer. Luckily, I stumbled upon a neat
little Javascript library called [TheaterJS](http://github.com/Zhouzi/TheaterJS).
The appeal of this project was three-fold:
- It was written in Javascript, a language I'm fairly familiar with
- It's codebase was small and condensed. Everything was in a single file of about
400 lines
- The owner had included an explicit TODO list at the bottom of features he'd like
to add but hadn't gotten two yet. A perfect place for a rookie like me!

## Step 2: Write Some Code

After I had found this project and settled on how I would contribute, I forked
the repo and got to work on my solution. This should be the fun part, and it was.
I did my best to maintain the same style as all the other code that had been
written, even if I didn't agree with it (**spaces are inferior to tabs, people**).
Aside from style issues, I made sure to avoid making any unnecessary globals
to avoid polluting existing code. Overall, I made sure I wrote **quality** code,
just like I would if I were working on my own project.

### Step 2a: Test Your Code

I can't think of anything more embarrassing than submitting a broken pull request,
so I made sure to thoroughly test my code after it had been fully integrated.
I found a few bugs, fixed them, tested some more, washed, rinsed, and repeat until
I was confident I had flawlessly implemented the feature.

## Step 3: Submit a Pull Request

Finally, I submitted my pull request and waited for the owner's response. At first,
he briefly looked at my code, said it looked good and that he was testing it locally.
Score! A few hours later he came back and told me he was experiencing a bug with
part of my implementation. Much to my dismay, there was indeed a bug in my code.
Luckily, I was able to fix it in a matter of seconds. I committed, pushed, and
waited for his response. After that, the owner and I had a brief discussion about
what different parts of my code did, and then he merged it with the existing repo!
Now, anyone who uses his library will be using some code that *I* wrote, and I
think that's pretty cool.

Hopefully, this is the first of many contributions to open source software. It's
definitely a satisfying experience that I would recommend to any programmer.
