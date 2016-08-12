---
layout: post
title: Chocolatey surprise or how to brake Internet
---

Chocolatey is famous package manager for Windows. Folks also name it "homebrew for Windows". Today, right before my vacation leave, I got Chocolatey surprise. Can you spot surprise in this code?

{% gist 08b1011c518fae22c632e47eb820b7a7 %}

Nothing wrong here. You can find [plenty](https://github.com/search?q=https%3A%2F%2Fchocolatey.org%2Finstall.ps1&ref=searchresults&type=Code&utf8=%E2%9C%93) of exactly the same files on GitHub. Now let me explain the issue.

Hint #1. Look at commit history:

![image](https://cloud.githubusercontent.com/assets/1477672/17631654/cea09a2a-6079-11e6-918a-087c37b63c94.png)

Hint #2. Look at release history:

![image](https://cloud.githubusercontent.com/assets/1477672/17631701/00ee6fa2-607a-11e6-8da4-328cb7100700.png)

Hint #3. Look at `https://chocolatey.org/install.ps1` and try to download latest package:

![image](https://cloud.githubusercontent.com/assets/1477672/17631792/62db3326-607a-11e6-82fe-7725d78ccfcc.png)

Type the link above to your browser and...

![image](https://cloud.githubusercontent.com/assets/1477672/17631849/9497c384-607a-11e6-8c7f-cad5092a471e.png)

Wow, there is automatic download of unreleased version on the Internet! But it's not that bad. The bad thing is new version has breaking changes. And this is how it breaks:

![image](https://cloud.githubusercontent.com/assets/1477672/17632278/4b89b970-607c-11e6-9df1-bcff96a3b7a3.png)

Just like that - "Empty checksums are no longer allowed by default for non-secure sources.". You have to use `--allowEmptyChecksums` now.

Well, it's definitely good idea, but the thing is that I _never_ seen deprecation warnings! This is a rule number one in this world today - before you break something, make sure you scream about it! You cannot just break it like this with no heads up!

The fix could be just to use `--allowEmptyChecksums` every time or just specify chocolatey version during installation. Unfortunately on Chocolatey website they don't encourage users to specify version. And seems like there is no any way to specify mask. In Ruby, Node and all other languages/frameworks we could use the mask `0.9.x`, but seems like there is no such mechanism in Chocolatey. So to fix it... just always specify the exact latest version:

{% gist 64b9287c73cd78af7a480432957406ad %}

Happy hacking!
