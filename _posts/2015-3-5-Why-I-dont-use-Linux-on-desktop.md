---
layout: post
title: Why I don't use Linux on desktop
---

I installed my first Linux distribution somewhere between 1995 and 2000. It was RedHat, not too bad, not too good, as usual. Soon Mandrake has been released in 1998 (17 years ago, holy sh8t!). It was the first attempt to make Linux friendly. 2015 is the year of Linux desktop. So what do we have for now?

* Unity is slow as hell on any moderate hardware. Turns any animated widgets to a film strip. Can't find anything with standard search dialog. Unity apps still can get stuck or shutdown in 20-30 seconds interval if you have 1-2 GB of RAM.

* One can't do essential things in KDE: create application shortcut on desktop, move it (yes, you can't move it with your mouse!), add localization without restart, change font in Konsole, all of the themes (even with high contrast or extremly faded) looks good only on IPS. On Russian locale installation en_RU.UTF-8 is installed instead of ru_RU.UTF-8. Because of that lots of Qt-apps are crashing without any diagnostics (coredump has sigsegv in messagebox). No search for help, `Ctrl + F` searches the current page only - freaking shame. Most popular things are hidden in 3rd and 4th level menus. No any system for settings: one place for localization, completely different place for keyboard layout. And all of that just slow. However, little bit less than Gnome.

I remember KDE2 and KDE3 times. Everything was always slow. But it least, it had no such RAM requirements. UI was more primitive, but simplicity was for good, it was less ugly.

And the question pops up here: why every Linux-on-desktop-year percentage of current users remains the same - 1%. Reason is easy: your buggy and completely unusable desktop products aren't a peice of cake. So if you use it you either forced by someone, or you just masochist.

Desktop Linux is usable only as a browser launcher and/or Windows terminal. That's result of 20 year progress.

A buddy of mine suggested to install XFCE to Debian stable (`sudo apt-get -y install xfce` if you're interested):

* It has right locale
* Icons are movable
* Office software runs well
* Minimum requirements to hardware

BUT:

* I don't wanna spend my time installing and configuring something, it's not 1998! MacOS and Windows just work out of the box.
* It has few issues. Well, all of them have issues. Desktop shell is something very labour intensive. None of Linux vendors are capable to offer well-written, bug-free Desktop software.
