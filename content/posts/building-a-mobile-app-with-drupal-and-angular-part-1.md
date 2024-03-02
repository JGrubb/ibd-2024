---
title: Building a mobile app with Drupal and Angular (part 1)
date: 2014-03-12
tags: 
  - angular
  - javascript
  - drupal
alias: 231-building-a-mobile-app-with-drupal-and-angular-part-1
---

### The beginning

I'll make the "what is Angular" part as brief as possible. 

Angular is a giant JavaScript framework from our friends at Google. It fits into a similar camp with Backbone, a framework that Drupalists will likely have heard of since it's included in D8 core. Angular aims to make it as easy as possible to build a single page CRUD app, and in my limited experience it succeeds.

I've never built anything with Backbone, but have the Peepcode series on it, and have been working heavily with Rails for a good while now. I'll avert a gush on Rails for the time being, but let's just say I really love the way that Rails doesn't really write any markup for you. It's much simpler to find your way through the request/response path, and in general I find developing with Rails to be a vastly more pleasant experience than developing with Drupal.

Alas, I've been a professional Drupal dev for about 4 years now. 

### The use case

I work at a publishing company. We publish about 26 different pubs, many of them still in print. Within the last year we finished a migration of all of our websites from various proprietary CMSs into a Drupal 7 multisite installation. The sites support desktop only at this point as we are a small company and resources are definitely constrained. (This also has it's upsides which we'll get to).

Last fall we rebuilt the [company website](http://www.advantagemedia.com/) from a static HTML site into Drupal 7. Since this was not a part of the multisite install, we were allowed to architect the site from scratch with my boss doing all the site building and myself doing all the theming. Mobile was a big priority this time, so I spent a good chunk of the development cycle implementing mobile-friendly behavior and presentation and generally getting a good feel for how to really do a responsive site. As an aside, for mobile/responsive site building and crafting responsive stylesheets, less is definitely more.

The end of this winter has brought a time table for offering a more accommodating mobile experience on our "brand sites".

### The dream

So my boss and his boss want a mobile site like "The financial Times has". If you have an iOS device, go to [app.ft.com](app.ft.com), and if you're a Drupal developer, try and get your head around how you'd pull that off, but try and forget that this is a post/series about Angular first. Pretend that you were going to try and pull that off in a more or less responsive fashion.

I spent a couple of days surveying the landscape for JS libraries that help out with touch behavior, and trying to figure out how to prefetch content so that when the user swipes from page to page or section to section, there wouldn't be a giant delay while the network does its thing transferring 1,000 lbs. of panels-driven markup. This was Monday and Tuesday of last week.

### A pause for reflection

My enthusiasm for the project already waning, I sat back and though about how we ought to be doing this thing. What they want is a mobile app, not a responsive website. 

The way that you implement a mobile app is not by loading a page of HTML markup with every user action, it's by loading the app once and then communicating with the server via JSON (or XML or whatever if you wanna get pedantic). This kind of thing is supremely easy to do with Rails mainly due to Rails's deep embrace of Rest 6 years ago totally getting ahead of, perhaps even enabling, this whole javascript app revolution in which we find ourselves. Outputting a particular resource as JSON is as simple a line of extra code to allow the controller to respond to a request with a different format.

### Step 1, Services

I'd never played with [Services](https://drupal.org/project/services), so I didn't know how easy it was to output a node as JSON. On Wednesday of last week, some time after lunch, I decided to find out. Turned out we already had Services installed for another use case that we just recently implemented (offloading our Feeds module aggregation to another open source project called Mule, but that's a whole other series of posts), so all I had to do was bumble through a tutorial on how to set up a Node endpoint.

In less that 5 minutes I had exactly what I needed set up in terms of dumping a node to JSON. I've been reading Lullabot's recent posts about their use of Angular, so the rest of this series will follow along as I learn how to use this giant beast of a JS framework to build the mobile app my boss' boss wants without a minimum of Drupal hackery.

The next post will pick up Thursday morning (as in, 6 days ago) where I first downloaded Angular into the project.