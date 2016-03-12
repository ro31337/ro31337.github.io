---
layout: post
title: MVC is actually MVA or You're not controller anymore
---

I'm reading a book of Yegor Bugayenko "[Elegant Objects](http://www.yegor256.com/elegant-objects.html)". Yegor has strong opinion that names ending with `-er` are really bad for your objects. The first one that came to my mind was... Controller.

I remember how it was confusing to me when I switched to MVC pattern a long time ago. Model-View-Controller. What it tries to control and how? Now I finally have answer.

But let's get back to the past and see where it comes from. Trygve Reenskaug introduces this pattern in 1979 while he was visiting Xerox PARC in Palo Alto. Since then this pattern was used primarily in UI, and was adopted by web community in the beginning of 2000.

It was said that controller manipulates the model. And user uses controller. Such a magic thing that controls, and can be used by users. Nobody explains how it can be used. In wiki it's just mentioned "can be used". Okay.

But let's get back to `-er`. What we can improve here if we want to get rid of `-er` at the end?

Basically, controller is set of actions. Just a bunch of methods that can be executed. And that's it, and should be nothing more. I don't like wiki explanation that controller controls model. What does it mean - "controls model"? Changing something? Prohibiting from change? It's unclear. If we can think this way, everything is controller, because there are a lot of places in our software, and each place "controls" another.

Yes, _controller_ is crappy name. But if you replace it with "set of actions", or just "actions", it immediately becomes more clear. Look at this:

Model View Actions. How it sounds? Or even Models-Views-Actions. So it's not MVC, it's MVA.

If we name it this way, we already know that we have Models, we have Views, and we have Actions. Actions are just methods. And we already know what does it mean. There can be anything in our method. And our routing now sounds more clear:

For `/users` url, redirect request to `UserActions`. It's fantastic, no more controllers, and we don't even need to rewrite our software, just inherit your new `Actions` from your already existing `Controller` in your favorite framework and here you go!

Moreover, when we have actions, we can use use adjective to let others know about our intentions, something like `UserPrivateActions` or `AuthenticatedUserActions`.
