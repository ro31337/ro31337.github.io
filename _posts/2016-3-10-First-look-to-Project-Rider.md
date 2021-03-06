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

I really appreciate the effort. I respect what JetBrains and Microsoft doing. But for now it looks like "Fix tools while others code".

Very often when you see fancy presentations, somebody can say that software is ready to use, and it's so cool, you just need to pay  for it. And when you have to spend significant amount of time just to make it work... I think we should be informed at least.

**UPD**

I twitted the link of this post to @JetBrainsRider yesterday, and got reply from them (see first comment). Thanks, JetBrains. I tried to google it more, and wanted to spend another hour of setting this, hopefully, wonderful IDE.

I found one [similar issue](https://youtrack.jetbrains.com/issue/RIDER-393). It was said to edit `~/.dnx/runtimes/dnx-mono.1.0.0-rc1-update1/bin/dnx` the weird way:

<img width="843" alt="screenshot at mar 11 16-28-27" src="https://cloud.githubusercontent.com/assets/1477672/13719372/858cb5ec-e7a9-11e5-9edb-c65f2d95ab17.png">

I tried to find a difference between these two files and here it is:

<img width="869" alt="screenshot at mar 11 16-30-02" src="https://cloud.githubusercontent.com/assets/1477672/13719375/858ff914-e7a9-11e5-88e8-60a94e438b1e.png">

So ignore the advice, and just update line 14 in `~/.dnx/runtimes/dnx-mono.1.0.0-rc1-update1/bin/dnx` from `exec mono...` to `exec /usr/local/bin/mono...`.

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

Second day, it doesn't work. There are more folks who desperately trying to make it work:

<img width="566" alt="screenshot at mar 11 16-42-58" src="https://cloud.githubusercontent.com/assets/1477672/13719376/85975b3c-e7a9-11e5-8618-fc919aba6dc6.png">

Conclusion: it is still doesn't work, but I hope it will. I can't name it developer's happiness now, but I hope things will be fixed soon. I really want to give it a try. Will keep you updated on my progress in this post.

### UPD2: How it works on Windows

Meanwhile, I tried to install Rider on Windows. I picked black scheme, so now you know - if screenshot is black, it's from Windows.

Unfortunately, we cannot create blank solution and add two projects as I described [here](http://stackoverflow.com/questions/32591764/create-build-and-run-solution-two-projects-library-and-tests-with-vscode). There is no such option "Blank solution":

![01](https://cloud.githubusercontent.com/assets/1477672/13731824/49aef516-e930-11e5-81b2-4260b7337b84.png)
![02](https://cloud.githubusercontent.com/assets/1477672/13731822/49ae8a68-e930-11e5-9b86-1db00cf8cf62.png)

Compare above to Visual Studio:

![03](https://cloud.githubusercontent.com/assets/1477672/13731826/49b1a5d6-e930-11e5-9847-f1a03b032850.png)

You can create "Empty Project" or "Console Application". Let's create console application (it's selected "Empty Project" on screenshot above, don't be confused, we need option above):

![04](https://cloud.githubusercontent.com/assets/1477672/13731821/49ac4686-e930-11e5-92b6-0c066d2ac888.png)

Solution was created. And console application project is the part of this solution. Compare to Visual Studio:

![05](https://cloud.githubusercontent.com/assets/1477672/13731823/49ae98a0-e930-11e5-860d-8c8a177a648c.png)

Wait, let's see now what "Empty Project" means in Rider. In VS we can create "Blank Solution" and "Empty Project":

![06](https://cloud.githubusercontent.com/assets/1477672/13731825/49b0b266-e930-11e5-8e57-64a27aebc60f.png)

Let's create "Empty Project" in Rider. I'm trying to create and... look at this:

![07](https://cloud.githubusercontent.com/assets/1477672/13731827/49c01968-e930-11e5-8be5-7842d9a439e7.png)

Path was not saved. It means every time I have to type it manually. VS saves relative path. `C:\Projects\` is the only path for my projects. Minor Rider issue.

![08](https://cloud.githubusercontent.com/assets/1477672/13731828/49c1ce70-e930-11e5-96f1-0224ebeea267.png)

Yes, "Empty Projects" means "Solution with empty project". Unfortunately there's no way to create just blank solution. So you have to delete existing one from Rider UI. For some reason after deletion it's still on the disk:

![09](https://cloud.githubusercontent.com/assets/1477672/13731829/49c44fce-e930-11e5-8bf8-dc47666c55d8.png)

And you need to delete it manually. There is any references to `HelloRider2\HelloRider2.csproj`. It's just abandoned. And UI looks like this:

![10](https://cloud.githubusercontent.com/assets/1477672/13731832/49c80eca-e930-11e5-9dd5-09320194a811.png)

`Close Project` menu item is confusing, because it's closing solution, and not the project. Here is how it looks in Rider and Visual Studio:

![11](https://cloud.githubusercontent.com/assets/1477672/13731831/49c670b0-e930-11e5-9c17-f75772efc180.png)
![12](https://cloud.githubusercontent.com/assets/1477672/13731830/49c5ad56-e930-11e5-8945-b33cadfdc44a.png)

Let's try to write and run the easiest ever program. When I type `Console.` for the second time, I expect to have `WriteLine` highlighted by default, because it was used last time. But Rider highlights very first item. It's sad:

![13](https://cloud.githubusercontent.com/assets/1477672/13731834/49d3f2da-e930-11e5-829d-35098f16a1ea.png)

It really affects productivity. But yes, it builds:

![14](https://cloud.githubusercontent.com/assets/1477672/13731833/49d3b19e-e930-11e5-9e0c-89af50aa4c31.png)

I can't say I don't like it. I really want to pay JetBrains for the product that can work on Mac OSX. But even on Windows it is not something I'd like to use now. I understand it's beta, but it's confusing, very fresh, and you have to be very enthusiastic and pain resistant if you want to use it. Let's wait for the final version and see if these problems have been fixed.

I also want to be able to create Test Project. Hopefully I will be able to do that, because I can't imagine how can I work without that. But yes, now it's beta. And release is on the way. And I will keep on trying to run things on Mac. But now it seems to me that C# for Mac is something very extreme now. Frameworks are RC, IDEs are not ready. I have a feeling that I won't be able to use it in the next year. And I wanna be wrong.
