---
title: Installing Drupal
date: 2016-03-29
tags: 
  - drupal
alias: 278-installing-drupal
---

See [the previous post in this series](posts/why-drupal) on getting started with Drupal

---

So welcome back, this is actually the most challenging part of this tutorial - installing Drupal. I'm assuming no prior web development experience, so the first part will be installing something to run your tutorial Drupal site on.

For this we'll be using a project called [MAMP](https://www.mamp.info/en/). MAMP is a package of software that makes it easy to set up Drupal (but not just Drupal) on your computer. I'll skip the deeper details for now, but head over to MAMP and download it. You can stick with the free version for now.

note: There are many basically equivalent packages out there for this purpose - XAMPP, Acquia Dev Desktop. If you'd prefer one of those feel free, but I'll be using MAMP because it's very simple and is what I got started with.

### What is MAMP?

This section can be safely skipped if you don't care.

MAMP is basically the same package of software that runs on any webhosting server, think Dreamhost or GoDaddy. It stands for (M)y (A)pache, (M)ySQL, and (P)hp. These are the three things that you need to make Drupal work. 

Apache is a "webserver". The webserver piece is the one that sits there and listens for incomcing requests from someone's browser (or bot). When a request comes in from somewhere, Apache figures out what that request is asking for and routes it to the correct item. It could be an image, which is very simple because it's just a file sitting on a disk. In this case, Apache grabs the file and returns it. This is called "the Request/Response" cycle, and is pretty much the slab that the internet was built on.

Sometimes however, the request is asking for a webpage in Drupal. This case is a bit more complex, and that's when PHP and MySQL come into play. PHP is a programming language. You might know that already, but Drupal is mostly written in PHP. That's why you need it for this tutorial. When you create a new page on your new site, the content of that page gets stored not as a file on a disk, but as a row in a database. MySQL is an exceptionally popular database, and the one that we'll us for this tutorial.

### On to the show

Hopefully MAMP has finished downloading by this point. Go through the installer, and when you get done you should be able to open MAMP in the same way that you'd open any other application. Once it starts up, you should have a screen that looks basically like this --

![Mamp opening screen](https://www.mamp.info/en/images/screenshots/en_mamp-start.jpg)

Once you click "Start Servers" you've done it! You've built your first webserver stack! 

Click into "Preferences".

Under the preferences option, you'll get some options to twiddle with. **Don't twiddle with them**, at least not yet. Option names will vary, but you're looking for the rightmost tab, it's either called "Apache" or "Webserver" in the most recent versions. Under that tab will be a most pertinent piece of information - the "Document Root".

> [!info] 
> Document Root - where the webserver will look for the files that it's trying to serve.

In a nutshell, once we download Drupal, we're going to put all it's files in that directory so make a note of where that directory is!

### Downloading Drupal!

Head on over to [Drupal.org](https://www.drupal.org/download) and download Drupal! At the time of this writing, that giant green button takes you to another screen where you are presented with ah choice. I was hoping to shield my readers from this, but if you're going to learn Drupal I guess now's as good a time as any to explain why this choice exists at all.

You may skip all of this.

---

### An interlude

Drupal has been around for nigh 12 years at this point. It was started in [a Dutch kid's](http://buytaert.net/) dorm room as more or less a message board for that dorm. Early in life it embraced the open source model for development, which means that other kids in his dorm were able to hack on it and add to it and improve on it and make it better for everybody.

Many years later Drupal was running some of the largest websites on the internet, and while it had been added on to and improved by thousands of developers by that point you could still find some of that 12 year old dorm code if you looked in the right places. Many people, your author included, felt disbelief that such code could be responsible for so much, yet at the same time took great comfort and pride that really anyone could learn this stuff just by following this code around. There truly was nothing really fancy about Drupal's codebase for a great many years. A few really smart patterns up front, followed diligently for years, and the rest is early internet history.

But time marches on, and with it evolution. Standards in computer engineering, common patterns for solving common problems, and much more complex needs on the web necessitated engaging with the wider PHP ecosystem. After all, the Easter Islands were once thriving communities, yet after time they thrived themselves right out of existence. Drupal wanted to avoid such a fate, so a decision was made in 2011 to replace some key pieces of Drupal's internal code with more modern code from a well known PHP framework - [Symfony](https://symfony.com/).

This made a heck of a lot of sense. Much of Drupal's aforementioned dorm code had very interesting, almost paleological qualities about the way that it solved problems as if "this was how our ancestors built a fire before we had matches", and newcomers to Drupal that \*did\* have a background in software development were often left scratching their heads to some of the decisions. In a nutshell, learning Drupal was easier for newcomers to web development than it was for established developers. 

Thus the rather controversial decision was made to standardize some of the very deepest parts of Drupal - those dealing with the "request/response" cycle.

Thus began a process that took 5 years and involved an almost complete rewrite of Drupal. This is both shocking and obvious in hindsight, since a complete rewrite is something you never, ever, ever want to do with a software project, yet once you modernize a piece of a system, the rest of the system looks that much more archaic.

The good part - Drupal is a modern and really impressive piece of software engineering, and includes many more features in the standard install that you're going to want on your site than previous versions. It's much more "batteries included" than older versions that required you to download and install lots of add ons to get it to do the things you really wanted it to do.

The bad part - much of the code that has been written by folks like you and me over the last decade doesn't work anymore. This is kinda brutal, but such is evolution. It also opens up something of a goldmine for new development opportunities within the Drupal ecosystem, but with that comes that learning to code for Drupal 8 will be a much different experience if you are new to building software. It'll require you to know what you're doing, which I most certainly didn't when I was learning Drupal (6).

The other good part - this entire tutorial can be done now with Drupal 8. 

So go ahead and download Drupal 8, but once you decide that Drupal is, in fact, for you you'll probably revisit this topic.

### Back to Drupal

So you've downloaded Drupal 8 - unzip it. You'll have a bunch of files and folders that look like this inside the newly unzipped directory - 

~~~bash
Downloads/drupal-8.0.5 [ tree -L 1 ] 4:50 PM
.
├── LICENSE.txt
├── README.txt
├── autoload.php
├── composer.json
├── composer.lock
├── core
├── example.gitignore
├── index.php
├── modules
├── profiles
├── robots.txt
├── sites
├── themes
├── update.php
├── vendor
└── web.config

6 directories, 10 files
~~~

All those files go in "The Docroot" - which is the path that you noted earlier in your MAMP preferences under Apache/Webserver/whatever. It'll end in `htdocs`, so something like `/Applications/MAMP/htdocs` if you're on a Mac, or whatever that screen says if you're not.

### The big payoff

Something always goes funny with people's computers, but at this point you should be able to navigate your browser to localhost:8888 and be greeted with the Drupal installation screen.

![Drupal 8 install screen](https://www.acquia.com/sites/default/files/installd8.png)

We're going to be choosing all the defaults for this tutorial, click through the language and the next option is for "installation profile", just choose Standard.

The next screen - "System Requirements" - is the tricky one. Ask below in the comments and we'll try to debug it together if you aren't allowed through. MAMP should have all this sorted out for you already, though so soldier on.

The next and basically final step is to give Drupal the connection credentials to your MySQL database. Those can be found on the welcome webpage if you click that middle button in MAMP. That'll take you to a screen that tells you for sure, but it should be something like 

~~~
user: root
pass: root
host: localhost
(open up the advanced options)
port: 8889
(leave the table prefix empty)
~~~

At this point, you're in. You've installed Drupal. There is one more configuration screen that you can plug all the answers into on your own. 

Save and continue on to [the fun part of the tutorial](posts/first-steps-drupal)!