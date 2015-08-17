---
layout: post
title: Making Something ugly
date: 2015-07-26 00:00:00
categories: programming
redirect_from: /blog/Making-Something-Ugly
---

I'm definitely an idea guy. I have ideas all the time. I think that most of them
suck, but the thing with ideas is that there's no way to know if they suck unless
you execute them. Generally, when you have an idea you want to make a prototype
as quickly as possible so you can decide where your idea falls on the scale of
suckiness (or greatness, if you're lucky). But like I said, I'm an idea guy, not
an execution guy. Executing is hard. Whenever I sat down to try to prototype an
idea one of the major roadblocks for me were graphics. It seemed like any low
level (aka powerful) language required lots of external libraries and gross stuff.
It was a pain, for me and probably for many others. So **ugly** is my attempt
at fixing that. I dreamed up **ugly** as a universal graphics library, a graphics
library that could be used on any machine, with any language, really quickly.
And I think I succeeded.

## Goals

Starting out, I had a few goals in mind when making ugly:

### Good revision history

I wanted to force myself to make small, frequent commits. This has a two-part
purpose. One, by keeping the commits small, it's easier for me to make progress
because it takes less time to make that little change here or add some safety
checks there. Secondly, it makes it easier to track down bugs. If I don't
notice a bug right away, it's easier to go back in the history to find out where
the bug first occured, and since the commits are small, it's usually very obvious
what change introduced the bug.

(There's also a third advantage: it makes writing project-recaps like this much
easier)

### Finish it

I had a really bad habit of not finishing projects. I wanted to finish **ugly**.
And I did. And I'm proud of myself for it. (Ironically, this blog post took
me several months to finish...)

## Planning

So I wanted to make **ugly** language and platform agnostic. That means it had
to be written in a language that everyone had. And what does every computer have?
A web browser. So I decided to make **ugly** with Javascript, doing all the
rendering in the browser. Unfortunately, there's really no good way to get data
from a user's machine to a Javascript front-end without introducing a server,
so I built a server implementation in node.js to forward data from the client
to the viewer. The interface was fairly simple: The client program or whatever
needed to be rendered writes data to the node.js server via `stdin`. The server
would validate incoming commands and then forward them to the browser-based
renderer using websockets.

At this point, there were a few things I wasn't certain of. I was mainly worried
about the latency that would be introduced by introducing the node.js
middle-man. It was important that the rendering happen as close to realtime
as possible. However, as I couldn't think of a better way to achieve what I
wanted, I decide to go ahead and try it out.

## Execution

*Note: This is pretty much me narrating my commit history. Yay version control!*

### Making connections

I started out just having the node.js server read lines from stdin. I then
introduced a basic logging module to handle logging to a file and/or `stdout`
and `stderr`. Next I cooked up the code for serving the rendering-client where
the actual drawing would happen and forwarded the lines read from stdin to the
client via websockets. At this point, all the connections were made and I just
had to add the logic.

### Protocol

The next thing I had to do was define the format in which **ugly** would expect
incoming commands to be. I decided on a fairly minimal syntax:

    command_name arg1 arg2 arg3 ...

I also introduced the concept of 'chunks' which grouped related commands:

    $CHUNK_NAME
    command_name arg1 arg2 ...
    command2_name arg1 ...
    ...
    $END_CHUNK

There would be two types of chunks: `CONFIG` chunks which would define settings
to be used throughout the render and `FRAME` chunks which would define the 
drawing commands to be used for a single frame.

### Validation

I decided that the server would be responsible for validating the commands so
that the client could assume it only received valid commands. The less
non-drawing work the client would have to do, the better. As I started writing
validation code for the commands, I realized that this was going to get real
gross real fast. I essentially had a block of code that looked like

{% highlight javascript %}
if (command_name = "command1")
    validateCommand1 (commandStr)
else if (command_name = "command2")
    validateCommand2 (commandStr)
...
{% endhighlight %}

And I planned to have well over 30 commands. I decided to split out the commands
into their own module. Each command be an object with a `validate` function.
This way the server, could simply query the command module, get the command,
and call its validate function:

{% highlight javascript %}
var command = commands[command_name];

if (command.validate ())
    sendToClient(command_str);
else
    error("Bad command!");
{% endhighlight %}

For one, this made it a tad bit easier to add commands (I didn't have to add an
extra "if" branch for each command), but it also turned out to make executing
the command easier on the client-side.

(*Note: in the end, instead of having
a validate function for the whole command, I had a validate function for each
argument, but this is a minor implementation detail; the gist remains the same*)

### Rendering

Once the command was validated server-side, I sent the full plaintext string to
the client via websocket. Here, I once again leveraged my command module. In
Javascript, suppose you write a function like so:

{% highlight javascript %}
object.myFunc = function (arg1, arg2) { // do stuff };
{% endhighlight %}

Conveniently, you can also access and execute this function like

{% highlight javascript %}
object["myFunc"].apply (object, [arg1, arg2]);
{% endhighlight %}

The key here being that the function is identified by a **string**. That meant
in my command module, I just needed to save the string representation associated
with each frame command. Additionally, I needed to get the arguments. This was
again easy because the arguments my plaintext command took were exactly the same
taken by the Javascript function. So I could apply all of my frame commands
in exactly the same way; all that changed was the data.


### Putting It All Together

To give you a better idea of how the commands are actually processed, I'll trace
the execution of one all the way through. Here's an example of how the
`clearRect` command is represented in the command module:

{% highlight javascript %}
clear_rect: {
    name: 'clearRect',
    type: commandTypes.METHOD
    params: [
        param ('x', paramTypes.FLOAT),
        param ('y', paramTypes.FLOAT),
        param ('width', paramTypes.BOUNDED_FLOAT (0,
                Number.MAX_VALUE)),
        param ('height', paramTypes.BOUNDED_FLOAT (0,
                Number.MAX_VALUE)),
    ]
}
{% endhighlight %}

And here's how that command would be provided:

    $FRAME
    ...
    clear_rect 0 0 100 250
    $END_FRAME

The server would first read in the full string (line 3 in the code block above).
It would then take the first "word" (`'clear_rect'`) and search for it in my
commands module (which would return the objct in the firstcode block above). From
here, the arguments of the plain text command would be validated. To do that, it
first sends all four parameters (`0, 0, 100, 250`) to the `FLOAT` `paramType`,
since it is the `paramType` of the first parameter in the parameter list. That parameter object
would validate it and return whichever arguments it didn't use. In this case,
the float parameter uses only the first parameter so it leaves `0`, `100`, and
`250` (parameter types don't always consume just one argument; a color param-type
for example consumes four: r, g, b, a). The remaining parameters then passed to the
next parameter objects until they are all validated. If no errors are encountered,
that command is sent to the client.

When the client receives the command, it again looks up the `clear_rect` object.
It then asks each of the command object for the Javascript value type.
For example, for a `paramTypes.FLOAT`, passing the string `'3'` would return
the value `3`. This argument list is then used to call the function:

{% highlight javascript %}
canvas["clear_rect"].apply (canvas, [0, 0, 100, 250]);
{% endhighlight %}

And we're done! Feel free to read through the code to see it in action.

## Final Thoughts

So that was the basic design and structure of **ugly**. I'm very pleased with the way I
was able to cleanly separate the data (the commands and their arguments) from the
execution. The amount of code reuse in this project is really quite impressive
in my opinion.

I think the most exciting thing about **ugly** was when I got the first (and as
of yet only) pull request. Just a few hours after posting the project
on Reddit and HackerNews, someone submitted an example that used *ugly* to
visualize Conway's Game of Life. It was super neat to see someone using code that
I wrote. That alone made the whole project worth it.

If **ugly** is something that sounds useful to you, go check it out on
[GitHub](http://github.com/williamg/ugly). As always, pull requests are welcome!
