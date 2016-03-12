---
layout: post
title: First look to Project Rider with C# / .NET on MAC OS X
---

[Project Rider](https://www.jetbrains.com/rider/) is in beta, and available for preview. I tried to install it to see if things are going easy enough, and if it's usable on Mac OSX El Capitan.

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

As for commercial software, I really appreciate the effort. I respect what JetBrains and Microsoft doing, and I'm giving away this motto to them for free:

> Fix tools while others code

When you see their fancy presentations, when they say that software is ready to use, and it's so cool, you just need to pay us for it, and when you have to spend ridiculous amount of time just to make it work... I think it's a lie. Yes, read it: they lie us.

**UPD**

I twitted the link of this post to @JetBrainsRider yesterday, and got no any support or comments. Thanks, JetBrains. So I tried to google it more, and wanted to spend another hour of setting this wonderful IDE and environment while others just code.

I found one [similar issue](https://youtrack.jetbrains.com/issue/RIDER-393). Officials said to edit `~/.dnx/runtimes/dnx-mono.1.0.0-rc1-update1/bin/dnx` the weird way:

<img width="843" alt="screenshot at mar 11 16-28-27" src="https://cloud.githubusercontent.com/assets/1477672/13719372/858cb5ec-e7a9-11e5-9edb-c65f2d95ab17.png">

What a weird advice. I tried to find a difference between these two files and here it is:

<img width="869" alt="screenshot at mar 11 16-30-02" src="https://cloud.githubusercontent.com/assets/1477672/13719375/858ff914-e7a9-11e5-88e8-60a94e438b1e.png">

Really? I will fix this fault of JetBrains support. Here is what you should do. Update line 14 in `~/.dnx/runtimes/dnx-mono.1.0.0-rc1-update1/bin/dnx` from `exec mono...` to `exec /usr/local/bin/mono...`.

Let's try it now:

<img width="979" alt="screenshot at mar 11 16-34-14" src="https://cloud.githubusercontent.com/assets/1477672/13719374/858e5488-e7a9-11e5-9a8d-73eed1a00d57.png">

Now it's better, but look at these erros, we still cannot build. We have three warnings:

* `/Users/Roman/other/KillMe/KillMe/KillMe.xproj:  warning : Could not find project file /usr/local/Cellar/mono/4.2.2.30/lib/mono/xbuild/Microsoft/VisualStudio/v14.0/DNX/Microsoft.DNX.Props, to import. Ignoring.`
* `/Users/Roman/other/KillMe/KillMe/KillMe.xproj:  warning : Could not find project file /usr/local/Cellar/mono/4.2.2.30/lib/mono/xbuild/Microsoft/VisualStudio/v14.0/DNX/Microsoft.DNX.targets, to import. Ignoring.`
* `/Users/Roman/other/KillMe/KillMe.sln:  warning : Project has unknown ToolsVersion '14.0'. Using the default tools version '4.0' instead.`

And one error:

* `/Users/Roman/other/KillMe/KillMe/KillMe.xproj: error : /Users/Roman/other/KillMe/KillMe/KillMe.xproj: /Users/Roman/other/KillMe/KillMe/KillMe.xproj could not import "$(VSToolsPath)\DNX\Microsoft.DNX.Props"`

Maybe we can just play with these switches:

<img width="345" alt="screenshot at mar 11 16-36-55" src="https://cloud.githubusercontent.com/assets/1477672/13719373/858e4e66-e7a9-11e5-9f04-7eef96c7d030.png">

Nope. Let's try to create another project. No luck:

<img width="984" alt="screenshot at mar 11 16-41-49" src="https://cloud.githubusercontent.com/assets/1477672/13719377/85995d6a-e7a9-11e5-8b80-301908d34f39.png">

Second day, it just doesn't work. There are more folks who desperately trying to make it work:

<img width="566" alt="screenshot at mar 11 16-42-58" src="https://cloud.githubusercontent.com/assets/1477672/13719376/85975b3c-e7a9-11e5-8618-fc919aba6dc6.png">

Conslusion: I'm scared. Not because of bugs, they're okay. I'm scared because the technology is so new and fresh, and at the same time marketed so aggressively. They should definitely wait before doing loud promises. If you do that, you'll be always releasing unusable software. Because you want to release new, you don't want to wait. Just admit that: yes, it's not ready. Please wait for another year until we can finish and deliver stable and usable product. But it just doesn't work these days. Today's reality: sell first, let customers just buy crappy things, give them support. With this approach you'll always fix tools while others code. It has nothing common with developer's happiness.
