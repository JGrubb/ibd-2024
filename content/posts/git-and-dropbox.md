---
title: Git and Dropbox
date: 2012-07-05
tags: 
  - devops
  - workflow
  - git
alias: 25-git-and-dropbox
---

I've just recently started to discover what Dropbox is really good at. I've had one for at least a year and almost never used it. The only thing I'd ever really used it for was client assets like PSDs and the like. I just discovered the Dropbox secret weapon - the symlink.

A symlink (short for symbolic link), if you don't know, is basically like a pointer to a folder/directory. It's a really nifty way to help you organize your filesystem. Say you use iTunes and for some reason you like to dig around in your iTunes music folder a lot. Rather than going into the Finder and drilling down into the folder from there, you could just create a symlink from your Desktop into that folder. Then, without actually moving your iTunes folder and possibly screwing things up, you've just created a shortcut to get into that folder.

So say I've got a client. Let's also say I have two different computers that I regularly work from. I've started using some code to test the project with that I don't want to check into their repo because they don't even really need to know that I'm doing automated testing with a tool of my choosing. So I add the directory that contains the tests to my .gitignore file. But (!), then I switch computers and need access to those test files. A git pull doesn't do anything because those files were never checked in. This struck me as the perfect use case for my dormant Dropbox folder.

I moved all the test files into a folder in my Dropbox. Then I created symlinks on both computers from that folder into the project directory. That way, they stay in sync across my two computers without needing to be checked in to the client's repo. Thank you Dropbox!