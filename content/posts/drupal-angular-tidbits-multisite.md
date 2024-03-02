---
title: Drupal Angular tidbits -- multisite
date: 2014-08-24
tags: 
  - angular
  - drupal
alias: 238-drupal-angular-tidbits-multisite
---

### Exposition

One of these days I'll get around to writing the post that's been in my head for 6 months now about how to get up and running with Angular in a Drupal setting. Today is not that day, however.

What I'd like to talk about today is a little hack that I came up with to allow me/us to maintain a single codebase to serve several different mobile apps.

#### Multisite Drupal

You're likely already familiar with this concept, and it has [its proponents](https://www.acquia.com/blog/power-drupal-multi-site-part-1-code-management) and [its detractors](https://www.getpantheon.com/blog/drupal-multisite-not-enterprise-grade). The second (anti) blog post has some good points -- it is a "hack" and you can paint yourself into a corner if your feature set starts to diverge between your "sites". Depending upon your business case however, it can be extremely useful. 

We use this [at my day job](http://www.advantagemedia.com/) to generally good effect. Someday there should be a blog post about the pitfalls of Features and why its siren song leads so many Drupal developers to crash their ships upon the rocky shores of circular dependencies and conflicted configurations, but this is not that post either.

#### Multisite Angular (to the point)

So how do you do this with Angular? In an Angular setup, you probably don't have any "moving pieces", ie - this thing is just HTML, JS, and CSS, talking to a server endpoint somewhere. That's cool! But it also means you don't really have the luxury of inspecting requests and setting environment variables on the server (like Drupal does it) to serve multisite. It has to happen in the browser. So what can you look at in the browser to set configuration for your multisite setup?

__The URL, of course!__

#### Angular "config" service

I'll just drop the code. 

~~~js
'use strict';

angular.module('mobileApp')
 .factory('Config', ['$window', function($window) {
 // Set up whatever variables you need here
 var test = {
 endpoint: 'http://testing-endpoint.com',
 randomVar: 'foo'
 };

 var prod = {
 endpoint: 'http://production-endpoint.com',
 randomVar: 'bar',
 otherVar: 'baz'
 };

 // A pointer object, basically. Keeps things
 // a little more organized
 var configs = {
 // production config
 'production-mobileapp.com': prod,
 // test config
 'testing-mobileapp.com': test,
 // dev config
 'localhost': test
 }

 return {
 fetch: function() {
 // 'configs' object returns whatever it's holding in the 
 // property with the key of 'window.location.hostname', 
 // which in this case is our config for this "site"
 var config = configs[$window.location.hostname];
 
 // Maybe you have some special snowflakes still
 // This can help you keep the divergence in check
 config.otherVar = config.otherVar || config.randomVar;
 

 return config;
 }
 }

 }]);
~~~

In the module that needs to know this stuff, you just pass in `Config` as a dependency and call `fetch()` on it.

~~~js
var siteVars = Config.fetch();
~~~

We use this setup to specify, for instance, the path to site specific CSS, or site specific DFP ad tag configuration.

~~~js
// In mainController.js, or wherever it makes sense

var stylePath = "/sites/" + siteVars.randomVar + "/styles.css"
~~~

And then that gets referenced in the head of the doc

~~~html

~~~

A hack? Yes. I've built two fairly large apps with this approach and have yet to paint myself into a corner though, so it's a fairly useful and rather robust hack, IMHO.