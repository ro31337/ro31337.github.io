---
layout: post
title: First look to Project Rider with C# / .NET on MAC OS X
---

Project rider is in beta, and available for preview. I tried to install it to see if things are going easy enough, and if it's usable on Mac OSX El Capitan.

<img width="1051" alt="screenshot at mar 10 16-51-24" src="https://cloud.githubusercontent.com/assets/1477672/13690665/37531f5a-e6e7-11e5-8ce7-f0ea1e637996.png">

This is how it looks when you try to to run Project Rider:

Yes, you need to follow the link. Right link is [this](http://docs.asp.net/en/latest/getting-started/installing-on-mac.html#install-asp-net-5-from-the-command-line), and it says "Install ASP .NET 5", which is little bit confusing - why ASP? I just need .Net. Okay, let's continue.

After few steps DNVM (Dot Net Version Manager) is ready:

<img width="912" alt="screenshot at mar 10 16-59-04" src="https://cloud.githubusercontent.com/assets/1477672/13690666/37557d72-e6e7-11e5-849f-7cfd45c68f8d.png">

But wait, is it rc? Yes. It just means .Net is something very new. Looking for stable tools? Maybe it's stable, but it's RC - if you like experimenting with thigs, like I do, it's okay. For production - well, it depends. Let's go further and install DNX (Dot Net Execution Environment).

There are two options and I don't understand what do I need:

* DNX for .NET Core
* DNX for Mono

It only says "By default DNVM will install DNX for Mono if no runtime is specified". Ah. I'm confused. So let's install mono, as it's suggested in docs:

```
brew install mono
```

And then install DNX for mono:

```
dnvm upgrade -r mono
```

<img width="677" alt="screenshot at mar 10 17-08-30" src="https://cloud.githubusercontent.com/assets/1477672/13690663/3751d9f6-e6e7-11e5-89ad-b682439f0d92.png">

Finally, let's restart Project Rider and see if it works!

<img width="1051" alt="screenshot at mar 10 17-10-12" src="https://cloud.githubusercontent.com/assets/1477672/13690664/3752c104-e6e7-11e5-83f5-1b9f28598c1b.png">

Nope. We've got error:

```
Project 'KillMe' load failed
Can't initialized DNX: DNX process '65309' exit unexpectedly with code 127
/Users/Roman/.dnx/runtimes/dnx-mono.1.0.0-rc1-update1/bin/dnx: line 14: exec: mono: not found
```

So I was just following official docs, installed a bunch of RC software, and it doesn't work. I'm not surprised, it's always like that when you touch something very brand new from Microsoft. I'm Certified by Microsoft since 2005, and I write programs with C# / .NET for more than 10 years, and the outcome is exactly the same. Fresh tools just don't work (but I still do like them). Maybe it's something about JetBrains and Project Rider? Let's find out.

Googling this error gives nothing. I suspect it's something about environment variables. Brew installer gives us a hint:

```
export MONO_GAC_PREFIX="/usr/local"
```

Let's add it. Nope, result is the same. I installed Mono via homebrew. Because it was said:

> Install Mono for OS X. Alternatively you can install Mono via Homebrew.

Let's try to install it from [official website](http://www.mono-project.com/docs/getting-started/install/mac/).

<img width="619" alt="screenshot at mar 10 17-25-20" src="https://cloud.githubusercontent.com/assets/1477672/13690668/37572136-e6e7-11e5-8e8c-07a27075415c.png">

Nope, doesn't work:

<img width="1050" alt="screenshot at mar 10 17-26-33" src="https://cloud.githubusercontent.com/assets/1477672/13690667/3755fbee-e6e7-11e5-88cb-910872deae3b.png">

Maybe reboot? Nope, the same thing. Conclusion: I was not able to run IDE and environment in reasonable amount of time. It's too fresh and too extreme for me. I leave it for you, young hackers. Leave your comment if you solved this!






 
