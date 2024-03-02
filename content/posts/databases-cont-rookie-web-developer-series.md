---
title: Databases cont. - the rookie web developer series
date: 2012-04-18
tags: 
  - databases
alias: 208-databases-cont-rookie-web-developer-series
---

*This is part of an IRC transcript between a buddy and I, wherein I try to explain a little bit about how the internet works and why knowing at least a little bit of database theory will go a long way in demystifying learning how to build stuff. (It's a technical term.) He's trying to learn a bit about Drupal and about how you build sites with it, so I'm going to tag him in on a project for a friend of mine who runs a yoga studio. Hers was [the first site I ever built](http://breathingroomcenter.com/), in Wordpress. I've been thinking about porting it to Drupal and having some more fun with it, as she'd kinda like a database of her registered students among a few other niceties that would be fairly easy to pull off in Drupal.*


---



grubb: And it basically works fine, but I wanted to have a bit more fun with it and integrate the calendar into the site  

[4:21pm] grubb: but it's pretty much a prefect easy Drupal site.  

[4:21pm] grubb: When I say "there'd be a couple of different content types  

[4:21pm] grubb: "  

[4:21pm] Keith__: ok  

[4:21pm] grubb: does that mean anything to you?  

[4:24pm] grubb: So I'll just go ahead and explain it  

[4:24pm] Keith__: ok, sorry multitasking  

[4:24pm] grubb: Pretty much everything you interact with on the internet is an interface to a database somewhere.  

[4:25pm] grubb: no sweat.  

[4:25pm] grubb: I assume you're familiar with super-basic database theory  

[4:26pm] grubb: basically a database is a bunch of data, and that data is ordered in a very structured way so that it's easy to tell a computer how to go get the specific data you're looking for  

[4:26pm] grubb: Example - http://ignoredbydinosaurs.com/  

[4:27pm] grubb: if you scroll to the bottom, the blog - that's one content type on my site, which means more or less that each one of those posts is located in one row of the "blog" table in the database.  

[4:27pm] grubb: I'm the only user on that site, but if there were more we'd each occupy one row in the "user" table in the database.  

[4:28pm] grubb: My portfolio is another content type - each portfolio entry is one row in the "portfolio" table in the database.  

[4:28pm] grubb: (This is a simplified explanation, but basically accurate)  

[4:28pm] grubb: If you go to Facebook, each user is a row in the "user" table in the FB database.  

[4:29pm] grubb: Each post on everybody's wall is a row in the "wall_posts" table in their database.  

[4:29pm] grubb: The "wall_posts" table in the database would have a column for the "post" and for the "user" who posted it.  

[4:30pm] Keith__: ok  

[4:30pm] grubb: So if I post something on my Facebook wall, that particular row would have "blah, blah blah" (whatever the post is) and my user_id  

[4:31pm] grubb: So by linking that table with the post on it with the table that contains my profile info (including my user_id), you can put all of that info onto my post on someone else's wall.  

[4:31pm] Keith__: ok  

[4:31pm] Keith__: based on user id  

[4:31pm] grubb: and that post would have all the meaningful shit that you want to see on FB  

[4:31pm] grubb: right  

[4:31pm] Keith__: ok  

[4:31pm] grubb: so the user_id is important  

[4:32pm] Keith__: what browser do you work in?  

[4:32pm] grubb: it appears not just in the user table, but also in the posts table so you can link the two together  

[4:32pm] grubb: I use Chrome  

[4:32pm] grubb: is this making sense?  

[4:32pm] Keith__: somewhat  

[4:32pm] grubb: the point is obviously that whenever you go to a web page, you're looking at HTML  

[4:32pm] grubb: (scuse me, it's not that obvious)  

[4:33pm] Keith__: ok  

[4:33pm] grubb: But there's not a mountain of programmers writing each page of HTML that anyone could possibly look at  

[4:33pm] grubb: on the entire internet  

[4:33pm] Keith__: right  

[4:34pm] grubb: When you go to your Facebook homepage, a computer on the other end of that is putting that particular page of HTML together "dynamically"  

[4:34pm] grubb: it says "okay, database, give me the 50 most recent posts from all of Keith Hick's friends. put the most recent one at the top"  

[4:35pm] grubb: and that is fundamentally what a database does  

[4:35pm] Keith__: ok  

[4:35pm] grubb: and so it hands that information back to the computer which then puts that specific page together for you  

[4:35pm] Keith__: right  

[4:35pm] grubb: If you go to the front page of my website it does the same thing  

[4:36pm] Keith__: complex filing system deciphered?  

[4:36pm] grubb: you mean this/path/or/wahtever?  

[4:36pm] grubb: to get that particular page?  

[4:36pm] Keith__: huh  

[4:36pm] Keith__: yeah  

[4:36pm] grubb: what do you mean?  

[4:36pm] Keith__: never mind  

[4:37pm] grubb: OK  

[4:37pm] Keith__: let's plan on rebuilding this site  

[4:37pm] grubb: OK  

grubb: so basically, start looking at most webpages as a thing that has been lovingly crafted for you by a computer at that specific point in time, because that's what a lot of them are  
