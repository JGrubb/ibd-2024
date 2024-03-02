---
title: The State of Bluegrass
date: 2012-06-29
tags: 
  - music
  - bluegrass
alias: 220-state-bluegrass
---

It's a toy I've been wanting to build for a while. I stole the domain name from Book fair and square, and have been quietly honing the skills to actually build it.

What I'd ultimately like to have is a site that is basically a collection of all current, working bluegrass bands. When you come to the front page, you get a list of bluegrass shows happening in your area. It does this by roughly guessing via your IP address. It has a list of shows in the database that it scrapes from somewhere. I used to think Facebook would come in handy for this, but it seems to have fallen somewhat out of favor as a place that bands keep updated with their shows. I tried Artist Data after that, but they have the most nebulous docs imaginable for the their API, which I'm not even sure is open.


So that's the big trick. Everything else is basically - add band, enter facebook and twitter username. It then gets bio and whatever other profile info from Facebook, whose API is somewhat open, and tweets from Twitter, whose API is way open. Yay Twitter.


It basically an experiment in modern web scraping. My first. It's built on [Sinatra](http://www.sinatrarb.com/), for those who care. the code lives here - <https://github.com/JGrubb/sinatra-facebook>


Anyway, the prototype - <http://sobg.johnnygrubb.com/>


It's easily breakable right now.

