---
title: The newby web programmer.  Episode 2.
date: 2010-01-30
tags: 
  - general development
alias: 60-the-newby-web-programmer-episode-2
---

So, you've gone and installed MAMP and WordPress on your computer, or if you wanna sound really hip you call it your "local machine". You've gone and started building out a wordpress site for your school because your current website is an outdated embarrassment. Wordpress is kind of the no brainer choice, or so your buddy told you, because it's super easy for anyone to use who's familiar with a word processor. You'd like to attempt to keep the content on your site fresh, so it sounds good to you. Then you have a meeting with your principle and your so-called webmaster to talk about getting some of the things you've done put up and made available to the world. Then they drop a little piece of info on you.


Your school's webhost doesn't do PHP, and they don't know if the district will let you move to another host. Let me tell you what this seemingly innocuous piece of information means.


It's means you're screwed.


PHP is a programming language. It's what's formally known as a scripted language. [I have a post here](posts/chapter-1b) that explains what that means. Wordpress is written in PHP. The advantages of a scripted language are what lead them to be used to write the majority of cool, interactive websites in the world. If you wanna sound hip you call them "web apps", since that's what they are. Site kind of implies a place, a static location that will be there when you come back and won't have changed much. An application is something you interact with. It does things for you, like letting you write news for the frontpage of your school's website that it will then display to the world without your having to go in and code HTML. Applications run on computers, or servers if they are web applications.


Compiled applications (the other kind besides scripted) have already been digested into machine code, which is to say 1s and 0s, prior to being run. This means that they are essentially frozen and you can't alter anything about the source code without going through a rigamarole. Scripted languages, in contrast, can be altered on the fly to add new features or fix bugs without having to be recompiled into machine code. This makes scripted languages perfect for constantly evolving web apps. The source code is right there on your webserver, and you can go and make changes to it. The downside is that you have to have some special programmatic gear installed on your server.


Scripted languages have to have their respective libraries of code installed in order to for them to be "interpreted" at "runtime". This whole long explanation is to tell you that since your webhost doesn't appear to have the appropriate libraries installed, your WordPress application is going to look like a bunch of gibberish to your webserver. You can upload it, but as soon as you point a browser at the index.php file, the server is going to throw a "what the fuck is this?", also called an error. That's why you had to install MAMP on your computer in the first place, remember? And unfortunately, installing PHP isn't an easy process, especially for someone who almost certainly doesn't have the proper "permissions" allotted to them in the operating system of the webserver.


I realize there's a bunch of gobbledeegook in here. I'll try to un-muddy the waters a bit in subsequent posts. Specific questions are most welcome. I should clarify that any webhosting company in the world is going to have PHP, and most likely a raft of other languages installed so that they can accommodate all types of clients and their disparately coded applications. That your school's webhost doesn't have PHP - the most widely used web programming language in the world for the last decade or so running - tells me that your district has their own hilariously outdated webservers set up in the furnace room of some office somewhere. We may be dead in the water, Jimmy...

