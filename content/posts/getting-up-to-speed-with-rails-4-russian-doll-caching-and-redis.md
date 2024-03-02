---
title: Getting up to speed with Rails 4 "russian doll" caching and Redis
date: 2014-02-19
tags: 
  - rails
  - devops
alias: 224-getting-up-to-speed-with-rails-4-russian-doll-caching-and-redis
---

## Russian Doll caching

It's a well branded name for something that makes total sense after reading one blog post. [The second half of this blog post](https://signalvnoise.com/posts/3112-how-basecamp-next-got-to-be-so-damn-fast-without-using-much-client-side-ui) will actually get you pretty much there.

If you're coming from a PHP/Drupal background like me, you might be surprised to find out that the database is *not* the bottleneck in Rails-land. Whereas on your typical Drupal page you might have anywhere from 50 to 1000 database queries being run, you'll be hard pressed to find a Rails app that comes anywhere near that number of DB calls being made. The Hobo companion, even on the front page (the heaviest page in terms of data so far) only runs about 8-10 queries.

What takes so long in Ruby land is view rendering.

I've witnessed this first hand on my job's Redmine install. I'd have thought that what was taking so long was the database, but what actually takes 95% of the time on each page load is the view rendering. I guess the Basecamp folks noticed the same things, so they went to work on how to speed that up.

### Fragment caching

There's always a blurb in evey article about Rails caching that has to do with "fragment caching". Basically, you cache little bits of each page as they're rendered, and the next page requests pull the rendered HTML from the cache to reassemble your page. It's simple, except that it's not. You've heard the old adage about the 2 hardest problems in computer science - cache invalidation is the PITA in this one. That basically means making sure (somehow) that you're not serving stale fragments when something has been updated in the meantime. I'm not sure what the old scheme was for taking care of this, but it wasn't friendly or intuitive.

### Fragment caching ++

The solution that they came up with involved making a digest of the actual object being rendered be the cache key.

~~~erb

All blog posts
--------------

<% cache [ "archive", @posts_by_year.first.first ] do %>
  <% @posts_by_year.each do |year| %>
    <% cache [ "archive", year[1].size ] do %>
      ### <%= year[0] %>


      <% year[1].each do |post| %>
        <% cache [ "archive", post ] do %>
        
        #### <%= link_to post.title, post_date_path_helper(post) %> - <%= post.created_at.strftime('%D') %>



        <% end %>
      <% end %>
    <% end %>
  <% end %>
<% end %>

~~~

This is the view that renders [my blog's index page](posts). I'm still getting the hang of how to name these cache fragments, but the idea is that you recursively wrap each rendered fragment on the page. If one item gets updated, the digest for that item changes, and the cache key for it changes as well. The next time the page is rendered, the value won't exist in the cache for that key (because the key has changed based on the digest of the object). It'll be stuck in the cache, and every fragment that wraps it will be invalidated as well. They'll be re-rendered, but rather than having to re-render everything on the page from scratch, the other items that haven't changed will be pulled from the cache. The vast majority of the page will not have changed, and will still be alive and well in the cache. In this way, the whole page can be 95% cached and only the parts that change will have to go through the whole trip to re-render.

It does still call your database to get the objects for digesting, but as we've already discussed, this is a small cost comparitively. Down the road there are solutions to this issue as well one your optimizations get to that point.

## Cache store

When I first started implementing caching on this site, I started off easy with Rack Cache. It's a simple HTTP cache that causes the whole page to be stored with a TTL time on it. The TTL is set in the controller with something like `expires_in 5.minutes, public: true`. Once I started moving into the fragment caching business, I moved out of Rack Cache and into using memcached as the store. It's easy to set up. So easy I'll probably never write a post about it. It just works.

It did, however, seem to take up a fair share of memory - as you'd expect from a memory cache store. I already had Redis running for queuing background jobs via Sidekiq though, so it occurred to me over dishes that I should give that a try. Turns out it's just as easy as memcached. Just swap out `gem 'dalli'` in your Gemfile for `gem 'redis-rails'` and change `config.cache_store = :dalli_store` to `config.cache_store = :redis_store`. It seriously doesn't get any easier. Redis is a lot like memcached, except that it has some more advanced fatures that I might never use. It also writes to disk every now and then so if you restart your box, Redis can keep the cache warm rather than losing everything it's stored.