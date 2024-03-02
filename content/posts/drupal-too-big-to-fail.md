---
title: Drupal -- too big to fail?
date: 2014-01-27
tags: 
  - drupal
alias: 2-drupal-too-big-to-fail
---

I'm sorry, but if there's one thing I love doing, it's taking Drupal down a peg.

---

I'm currently investigating doing some real time push notification work on my company's sites to make them more buzzword compliant. This is great because it finally gives me a bona fide excuse to dig into a tech that I've been wanting to find a nice small use case for for a long time - [Nodejs](http://nodejs.org/). We could easily outsource this piece to something like [Pusher](http://pusher.com/), but we outsource a lot of pieces of our architecture, and with each piece comes a little accrual of technical debt. We might be able to skate by without ever having to pay it off, but we've just recently gone through a large exercise with Exact Target, our email service provider, that was vastly less than smooth. So buy in to investigate the merits of keeping it in house is what I got.

Now real time notifications isn't exactly setting up a Wordpress blog, but it's also a pretty well solved problem in this day and age, and the one use case where Node just absolutely earns its bread, so we're looking into Websockets and tying into some simple events in the Drupal stack. I was just thinking about the presentation I went to at DrupalCampNJ a couple years ago by the author of the Nodejs module. His was mainly a plumbing job to expose some of Drupal's hook lifecycle to the Node event loop, and may very well end up being something we leverage, but this phrase popped in my head.

**The last thing on Earth I want to do it to couple more shit into Drupal**. What I want to do is to break Drupal into little pieces, but it just keeps getting bigger and bigger. Not unlike how Bear Stearns and Wachovia got absorbed into larger banks that then became even larger banks, Drupal is \*too big to fail\*. I think we're in the twilight of the monolithic CMS age, but plenty of folks are betting that we aren't. I suspect we'll all have jobs one way or another, but something is just fundamentally unsound about the approach with D8. To me. I am a terrible programmer by the way, vastly inferior to all core Drupal devs, lower than dirt. Fair disclosure. 