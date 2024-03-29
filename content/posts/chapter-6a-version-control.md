---
title: Chapter 6a - Version Control
date: 2009-10-14
tags:
  - general-development
aliases:
  - 117-chapter-6a-version-control
---

So it's been a while, eh? Not too much in the mood to write about what I've learned when most of what I've learned is how much I have to learn, y'know? Anyway, I've been back into Ruby on Rails lately, since Ruby has got to be the dead simplest real programming language out there. Oh yeah, and I have a major project cooking on Rails, but I'll elaborate later. Let's talk about version control...


Let's pretend you're typing that last term paper in college. This is the modern era, so we're using a word processing program. That program has eaten a few of our papers over the course of our college career, so we know to save our paper often. This is a huge paper, so we've also decided that it'd be prudent to save it under different names as it progresses so that we can go back in time to an earlier version with no hassle, y'know just in case we stray too far from the objective in the future. This is basically the same idea behind version control.


When dudes or dudettes are sitting at their text editors coding away at whatever they're coding, in whatever language, they're typing characters into a keyboard, just like I am now. The difference with software is that there are usually numerous files that all relate to each other in some way, and a change in any one of those files will impact the entire application. So say you make one little change here and another little change there, and then you skip over to another file and make a change there, and then you skip over to another file and make a couple of changes there. How the hell are you supposed to keep track of all that? Version control.


Whatever the flavor - [CVS](http://www.nongnu.org/cvs/), [Subversion](http://subversion.tigris.org/), [Git](http://git-scm.com/) - all version control systems track changes made to the files in your fledging (or production) application. The manner in which they do this varies from system to system, and they all have their proponents and detractors, but that you have GOT to be familiar with at least one of them is beyond dispute. These will give you the ability to hack around all you want in the comfort of knowing that you can roll these changes back if you're not getting anywhere.


Now, the only problem with all of these version control systems is that they don't track changes to databases. The reason that this is a big deal is that the vast majority of large, modern, dynamic websites store a lot of data in their database. Some content management systems like Drupal actually store templates that determine how the website gets rendered to your browser in their database by default. You can change this if you know what you're doing, and that assumes a lot. Nothing is going to change the fact that many database powered websites have to occasionally make changes to the layout (schema) of their database to incorporate new features and the like. The new Like feature on that site probably has it's value stored in a column somewhere in a database. What if that feature sucks or is buggy? How can you roll back a database to an earlier state without using up your quota of blood pressure meds too soon? Under traditional version control systems, you can't.


Luckily, someone has figured this out for us. It's called a "migration". I'll be back. I promise...

