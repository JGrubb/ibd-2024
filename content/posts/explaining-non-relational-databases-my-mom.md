---
title: Explaining Non-Relational Databases To My Mom
date: 2013-05-17
tags: 
  - databases
  - theory
alias: 210-explaining-non-relational-databases-my-mom
---

I was on the phone with Mom yesterday, and we got to talking about technology - a thing that actually happens fairly frequently. Being an only kid, she's genuinely interested in everything that I do and it's been helpful to have someone who's mostly non-technical to bounce explanations off of when I'm getting my head around a new piece of gear. 

The piece of gear that I was explaining the other day was something called Mongo DB. Mongo's parent company is called 10gen, and they landed on the startup scene about 5 years ago or so with their flagship product, Mongo DB. Mongo is currently the pre-eminent player in the "NoSQL" database market. The NoSQL flavor of databases has come en vogue in the last few years in certain technology sectors, primarily ones that are evolving so quickly that having to slow down to put forethought into your data store and how it's going to be structured might literally be the difference between your whole company suceeding or not. That whole sentence will make more sense in a minute, but first it'd be helpful to understand how a traditional, "relational" database works.

## The Relational model

The relational model of storing data has been around for more than 40 years. Wikipedia, of course, has a great article - [http://en.wikipedia.org/wiki/Relational_database](http://en.wikipedia.org/wiki/Relational_database) - giving a technical overview, and I wrote my own article 4 years ago while trying to get my own head around the concept - [http://www.ignoredbydinosaurs.com/2009/04/chapter2-databases](http://www.ignoredbydinosaurs.com/2009/04/chapter2-databases). Wow, that's hilarious reading that article actually, since my "Railroad Earth setlist database" example is well underway - [http://www.hobocompanion.org/](http://www.hobocompanion.org/). So yeah, read that second one for the idiot's guide.

The classic example I gave to my mom was that of a common blog. You have a table for all your posts. Each post in that table has a row, and each row has something like a numeric ID, and the text of that post. When you want to read a blog post, the URL in that address bar of your says something to the effect of "give me post #1". Whatever blog hosting software your using then turns around to the database behind it and says "select everything from the posts table where the ID of the post is 1". The database hands back the post, the software renders some HTML out of it and sends it back to your browser. This is the simplest example of your interacting with a database. 

The relational model typically comes into play when you visit a blog that has comments. Now you don't just have a Posts table, but you also have a Comments table. That Comments table will most likely have the same ID column, and a column called "body" or something like that for storing the text of the comment. However, this table will also have a column called something like "post_id", and what gets stuffed in that column is the (you guessed it) ID of the blog post that this comment "relates" to. So now when your reader comes by, the blog software turns around to the database and asks for two things this time - "select everything from the posts table where the ID of the post is 1", and then "select everything from the comments table where the post_id is 1". This second "query", if you're lucky enough to write something that people respond to, will return a list of comments, an _array_ if you will, that your blog software will then convert to HTML and append to your blog post in the form of the comments section. Pretty simple, _or is it_?

## Issues with the relational model

For the purposes of this simplistic example, this hopefully isn't that hard to get your head around. "But", you might be wondering, "does that really make sense to store blog posts over here and comments over there? They're clearly related. When are you ever going to be examining a post's comments without wanting that post also?" 

Very good. And this is a prime example of when a "non-relational" or "NoSQL" database might make a lot of sense.

## The non-relational model

Let's stick with the same example, the blog post and comments, but let's think about how to "model" this in a non-relational way. By the way, if you're still with me, you have a deeper technical understanding than 99% of everybody around you.

The non-relational data model would look more like a sheet of paper. In fact, the concept of one entity and all the data that pertains to that one entity is known in Mongo as a "document", so truly this is a decent way to think about it.

The way that your blogging software interacts with this blog post is theoretically a lot simpler in a non-relational database. Request comes in, blogging software turns around to Mongo and says "Please give me back this specific post and everything related to it", in this case a listing of comments. **BUT**, that's not all. Since we're not forced to be too uptight about having to define how the data is structured beforehand, what if we want to tag that post with an arbitrary number of categories? No problem, stick them on the same document and when blog software says "gimme everything on Post #1", the tags, the comments, and any other randomly associated data come back with it. Your software doesn't need to know ahead of time, you don't need to know ahead of time. It all just kind of works, provided you know what you're doing on the software level. 

This amount of flexibility is what makes Mongo a very popular "data store" with quickly moving techonology companies that might be chasing an idea that changes from week to week or day to day. You don't have to rearchitect your entire system when you discover this particular use case down the road where you need to stick some "extra data" on this particular "thing".

## Issues with the non-relational model

It seems obvious that for this example a non-relational database makes a lot of sense, but like any useful tool, it has it's limits.

For this example, let's use a grocery list. You can model the grocery list in a lot the same way - a piece of paper, you write the items you want on there. But let's say, for the purposes of this example, you figure out after a couple of months that you want to keep track of how many loaves of bread you actually bought last year. Well, with the non-relational model you might literally have to go through every list and count each loaf individually (\*simplistic example alert\*), whereas had you modeled this in a relational way, you could get that count back almost instantly. Yes, it'd be a bit more work on the front end - essentially you'd have a Lists table and an Items table. But, modelled correctly, you'd also have a table in the middle called a "join table" that allows you to associate any number of items with any number of lists. After a while, were you truly the hacker, you could probably write some code that'd predict what you need to put on that list without even having to put it on there yourself, based strictly off of patterns that are easily discernable in a relational database.

That's why the Hobo Companion has a relational database behind it. It's super easy to count how many and which shows each user was at (2000+ shows tagged between about 30 users so far). It's super easy to count [the number of times they've played Mighty River](http://www.hobocompanion.org/songs/mighty-river "Mighty River - Railroad Earth"). It's super easy to count the number of times that I played Mighty River (somewhere around 260 times). It's super easy to figure out [where in the setlist they typically put Dandelion Wine](http://www.hobocompanion.org/songs/dandelion-wine "Dandelion Wine - Railroad Earth") (Either first, or at the end of the first set). These calculations are far from impossible in a non-relational database, but they are also pretty far from trivial.

In situations where you absolutely want to be really uptight about your data and how it's structured - think bank accounts as the classic example - a relational database is absolutely required, not just because it's theoretically better suited for the job, but it's also a "mature technology" that's older than I am. 

The wonderful point here is that we actually have decent options depending on what it is we are actually trying to do. Startup hackers love Mongo precisely because it lets you move fast. You can rock out an application in a weekend without having to spend most of one day setting up the equipment in the studio first.

Thanks for sticking with me.