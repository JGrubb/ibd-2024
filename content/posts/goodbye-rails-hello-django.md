---
title: Goodbye Rails, Hello Django
date: 2016-03-19
tags: 
  - general development
  - python
  - django
alias: 272-goodbye-rails-hello-django
---
So here it is. The last version of this blog - a Rails frontend to a Postgres backend - actually stood for almost 2 and a half years. I think that's probably a record. 

In keeping with my decided new theme for this blog however, I've decided to rewrite the thing in Django. Not that you can't google it yourself, but Django is (at a high level) basically the Python version of Rails. Actually, it's basically the Python version of every MVC web framework. It's been around for 10 years, so it is far from the hot-new-thing. I've finally been doing this for long enough that I shy away from the hot-new-thing and actively seek out boring, tested solutions to problems. 

At work we've begun a small project that we were targeting to build on Drupal 8. Faced with the timeframe, the relative lack of basic modules for building Drupal 8 sites, and the learning curve for the code that we'd inevitably have to write on our own I pitched the idea to my team to try something completely different. I prefaced it with "this is a terrible idea, so raise your hand at any point", but surprisingly they were all amenable. We all spent a day going through the amazing tutorial and the amazing documentation and they were still on board. So I decided to rebuild this blog to take the training wheels off and give us all some reference code for some of the simple features that weren't walked through in the tutorial - taxonomy, sitemaps, extending templates, etc.

Amazingly it took me all of 4 hours to rebuild the whole thing and migrate the data from one PG schema into the one that Django wants to use. Django is even easier to use than Rails - a fact that blew my mind once I started playing with it. 

The deployment story however, is a shit show. I spent as many days trying to get this thing up on a Digital Ocean server as I spent hours building the application in the first place. I'm hoping to find that there is an easier, more modern means for serving Python apps in 2016 after some more digging. 

Anyway, thanks for stopping by!