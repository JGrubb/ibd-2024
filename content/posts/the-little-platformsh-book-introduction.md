---
title: The Little Platform.sh Book - Introduction
date: 2017-08-23
tags: 
  - general development
  - platform.sh
alias: 310-the-little-platformsh-book-introduction
---

The internet is hard these days. 

It started simply enough - for instance, all you really needed was a Geocities account and some initiative to learn HTML and you could have your own place to put whatever you wanted and make it available to the entire world. From such simple seeds, complex structures did grow..

Geocities was permanently shut down in 2009, at once both a tragedy for the loss of so much content, so much _history_, and yet also a wake-up call for so many of us that we needed to have control over our own content, applications, and businesses. Many of us chose to host our own websites so that a seemingly arbitrary decision from some faceless corporate power couldn't upend overnight what we'd created over years. But that decision also made things a little more complicated.

At that point all you really needed was a web hosting account somewhere with Apache HTTPD installed as a web server. Then you could edit and upload your HTML just as before, only this time it couldn't be taken away from you because you were (more) in control of the setting. 

Somewhere along the way you were likely introduced to something like Wordpress or Drupal or Ruby on Rails, which were all essentially frontends to some kind of database, and that database is where you would store your content. This was a wonderful development, not only enabling non-technical users to publish content to the web without knowing anything about HTML or FTP, but also for small businesses to be able to create their first eCommerce stores online and take advantage of an entire global market. Again, this was the march of technological progress creating new opportunities for primitive man to make use of highly advanced tools without having a Computer Science degree.

But, as the saying goes, with great power comes great responsibility. The responsibility in this case is that of having to set up and maintain your own web hosting infrastructure. Some of us like this kind of work - setting up and managing fleets of servers with all manner of different pieces of software on them to serve the world's internet needs - but some of us really just like writing code and building websites and applications.

Platform.sh is a new breed of hosting service, and was created expressly for this second group of technologists. 

Platform.sh gives you an incredibly flexible set of tools with which you can build and deploy a huge range of different types of applications to the world with the click of a button or a push to a Git repo. Platform.sh currently has support for PHP, Ruby, Python, and Node.js with other runtimes like Go and Java either in public beta or in planning mode internally.

Platform.sh was also architected from the ground up to fulfill the promise of Git as a codebase management tool. No longer are your working feature branches trapped on your machine or in a remote repo with no context to make them live (and testable) for your teammates. Platform.sh will provision a fully functional, completely segregated hosting environment for each one of your Git branches, with all of the data and uploaded files that your app's codebase needs to be a fully functioning application.

Lastly, Platform.sh was designed to grow with your project as your project's needs evolve. The days of filing support tickets to have a PHP extension or a new database server installed are effectively over. Not only will we provision all of your application's software dependencies - Redis, MySQL, ElasticSearch, and many others - but you can choose from many different versions of each of these dependencies. Want to see how `ruby:2.4` or `python:3.6` or `postgresql:9.6` or `php:7.2-rc` improves your app's performance? We always endeavor to provide the latest versions of each so that upgrading the underlying software on which your application runs is as simple and painless as changing a line of configuration. 

If this sounds like something that might take some of the tedium out of your development day or possibly increase the velocity with which your business can bring new ideas and features to your customers then please, read on. In the coming chapters we'll walk you through getting started with Platform.sh by stepping you through setting up your first project and deploying a simple app with a few commands. After that we'll dive deep into more advanced topics such as -

- Configuring your project with YAML
  - routes.yaml
  - services.yaml
  - .platform.app.yaml or "app yaml"
- Managing and interacting with various administrative functions of your project both via the user interface as well as the Platform CLI
  - branching
  - merging
  - backups
- How all of this seeming magic works under the hood
  - containers
  - environment variables
  - copy on write

So, welcome to The Little Platform.sh Book! We sincerely hope to make internet-ing a little bit easier for you and your team.