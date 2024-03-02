---
title: Memcached - a really interesting piece of gear
date: 2015-10-02
tags: 
  - devops
alias: 265-memcached-a-really-interesting-piece-of-gear
---

So this is just another letter to my younger self, straightening out some mental inconsistencies with how I used to think Memcached worked. Much of this will be in the context of Drupal, since much of my work experience is in the context of Drupal. Memcached is obviously not a Drupal specific construct though.

### Expository

The first time I ever installed it was at the behest of my senior dev, who suggested just installing it on my laptop and giving it 64M of memory. Drupal is slow as molasses if you don't enable caching, so out of the box it actually comes with a pretty intelligent caching story. By default however, it caches everything into the database. This is "less than ideal" for sure, but since Drupal came up a long time ago in the shared hosting days, and since you could never really know what resources were going to be on the server in the first place it made sense to use what you knew would be there - namely MySQL.

This is not ideal since you're still hitting the database - often the bottleneck with Drupal - but it lightens the load significantly from the default and will definitely keep your site up under load, to a point.

One of the first places you start looking for performance improvements is in moving that out to something a little more responsive and purpose built. Redis is a newer option, but the old reliable standby is Memcached. Drupal has a bunch of tables in the database that start with cache_, and (simplified) they basically all have the format of (cache key)/(value). Love those out into Memcached and rather than hitting the DB, you're hitting memory. This is ideal, since looking something up in RAM is orders of magnitude faster than calling the DB. 

This is why big boy sites use Memcached.

### Beginning explorations

A bug appeared months ago on our websites. An editor would make a change to a piece of content, say bold an word, and upon saving the piece of content, they would frequently see the old version of the article and not see the change they just made. Obviously this is really annoying, because then they have to go and redo the change they just made, which then would usually work. 

This got really interesting when I discovered that clearing the cache on the site would then make the change appear. Clearly this was an issue in the cache layer somewhere.

We used to use a big name hosting vendor who built the servers for us, and Memcached was installed on every webserver and given 512M to work with. I knew that the load balancer would route authenticated traffic to the same webserver, so this lead to my mistaken notion that each webserver had it's own instance of Memcached to work with and that if the editor would hit a different one on saving the page, perhaps they were getting an old version of the article.

This is **not** how Memcached works, as it turns out.

### Go to Memcached.org

So the introductory page of [http://memcached.org/](http://memcached.org/) says 

> Free & open source, high-performance, distributed memory object caching system

What that means for me is that my mental model of each webserver having it's own pool and being unaware of the others was incorrect. What really happens is that each server you add to the pool adds to the overall cache size, and objects are distributed among them only once. I thought we had 4 512M instances of Memcached, but we really had 1 2G pool.

The [wiki](https://code.google.com/p/memcached/wiki) has some interesting notes on the the design paradigms that are worth quoting.

> The server does not care what your data looks like. Items are made up of a key, an expiration time, optional flags, and raw data.

Funny, so basically the exact same schema as the cache tables in the Drupal database. That's handy.

> Memcached servers are generally unaware of each other. There is no crosstalk, no syncronization, no broadcasting. The lack of interconnections means adding more servers will usually add more capacity as you expect.

> For everything it can, memcached commands are O(1).

So this means that means that it should basically scale infinitely with the same performance. Whether you have 32M on your laptop, or 48G across 6 servers as we have now in production, the lookup time is constant for a piece of cached data.

### What about the problem?

I actually just solved it yesterday. It was this - [https://www.drupal.org/node/1679344](https://www.drupal.org/node/1679344). Learned a hell of a lot about caching in Drupal and caching in general in the last 6 months before really hunkering down to figure this one out.