---
title: How (and why) to set up your Mac for Rails development - part 1
date: 2012-07-01
tags: 
  - rails
  - devops
alias: 23-how-and-why-set-your-mac-rails-development-part-1
---

The short version -

<https://github.com/JGrubb/laptop>

---


The longer version -


Setting up your Macintosh for Rails development is actually sort of like Rail development itself - you have to at least kind of know about a lot of different things before you can really get anywhere. I'd say I'm new to Rails development even though I've been poking at it since 2.1. 2.1 is the first version I remember after I first bought a Mac and started trying to teach myself to "program". It's taken me until 3.2 to come back to Rails with enough of a rounded web development skill set to really be able to fly around and build the things that have been locked up in my head for the last 4 years. I've written about this before, as have many others, but Rails isn't exactly a framework for beginners. That is, it requires you to have at least some idea of what you're trying to do before it will let you sit down and do it. Many of us probably grew up programming in Basic, but some of us might have gotten sidetracked into other vocations besides software development. the world has come quite a ways since then. So this post, or series maybe, will be an attempt to teach you not only what to install, but why.

---

### Step 0 - the compiler


If you are on a Mac, you are lucky to already have a rather large lot of tools that software developers use already installed. But they haven't given you everything. Some of the ones that will come in handy down the line as you get further in to this process will have to be installed by you. This will toughen you up and help you get more acquainted with a side of your computer that perhaps you didn't even know about. The world (and by that I mean the guy who made up Homebrew) has made life a lot easier for the Mac-using developer, but in order to access all of those goodies, you'll need to install a compiler. A compiler is basically a piece of software that builds other software. 


One of the first pieces of software that you're going to build will be the Ruby language itself, but first things first.


### XCode


If you're on Lion or the latest Snow Leopard, you can open up the App Store and search for "XCode". XCode is the official Apple development environment. With it you can build iPhone apps, applications for the Macintosh, and really pretty much anything that isn't specifically meant to be run on Windows. It's an enormous download, about 4GB recently. 


After you download it, open it up, open the Preferences, and find the extra downloads part. What you're looking for is the "Command Line Tools" bundle. It's in one of the tabs toward the right. Download and install. Should be fairly simple. Just to be safe, restart your computer. Once it's back on open the Terminal.app (get used to the terminal, you'll be there a lot) and type `which gcc`. If it doesn't spit anything back at you, you haven't installed the compiler. Figure it out and come back for the next step - the package manager.