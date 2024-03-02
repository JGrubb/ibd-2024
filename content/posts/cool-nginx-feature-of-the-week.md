---
title: Cool Nginx feature of the week
date: 2014-08-14
tags: 
  - devops
  - general development
  - angular
alias: 236-cool-nginx-feature-of-the-week
---

## Nginx configs

So I recently had a couple of seemingly disparate tasks come across my desk. We recently launched a HMTL mobile app, an Angular front end to our Drupal sites. We decided to completely decouple the mobile app from the Drupal codebase after a fairly long exploratory period trying out different approaches.

When launch day finally came, we set up the mobile app at `app.$$BRAND.com`, with our main sites at `www.$$BRAND.com`. Acquia has this cool feature in their Varnish layer that will filter user-agent strings for ones that match a set of "mobile" user agents that they have defined in a central file. So flipping folks on iPhones over to the mobile site was a piece of cake. What I forgot was that the same logic wouldn't be triggered for the reverse -- flipping desktop users to the desktop version of the site from app.$BRAND.com. (Our mobile app is hosted on our own, not with Acquia).

I already had a big list of regexes to test mobile user agent strings with (thanks Acquia!), so the trick was to recreate that in Nginx, the webserver for our mobile app.

Not wanting to do a bunch of evil `if {}` statements in the Nginx configs, I cast about for a more elegant solution, eventually stumbling upon `map`.

## The Nginx map module

[http://nginx.org/en/docs/http/ngx_http_map_module.html](http://nginx.org/en/docs/http/ngx_http_map_module.html).

So basically what this does is to test any [arbitrary Nginx variable](http://nginx.org/en/docs/varindex.html) for some condition, and spit out a custom variable that you can user in your config. An example ---

~~~
map $http_user_agent $device_redirect {
  default "desktop";
  ~(?i)ip(hone|od) "mobile";
  ~(?i)android.\*(mobile|mini) "mobile";
  ~Mobile.+Firefox "mobile";
  ~^HTC "mobile";
  ~Fennec "mobile";
  ~IEMobile "mobile";
  ~BB10 "mobile";
  ~SymbianOS.\*AppleWebKit "mobile";
  ~Opera\sMobi "mobile";
}
~~~

This takes a look at the incoming user agent string (fun fact -- grab any request header with `$http_NAME_OF_HEADER`) and compares it against a set of regexes. If one of them is a match, then the `$device_redirect` variable gets set to "mobile", otherwise, it's set to the default of "desktop". This gets used later in the config --

~~~
if ($device_redirect = "desktop") {
  return 301 $scheme://$desktop_host$request_uri;
}
~~~

In other words, if the user agent doesn't match one of those regexes, redirect the user to the desktop site. Pretty neat!

As a side note, does anyone else think it's weird that the comparison syntax in that if statement only uses one '='? But yeah, [that's the right way](http://wiki.nginx.org/NginxHttpRewriteModule#if). 

## Later that day, on a different Angular app

So that mobile app and this one that I'm about to talk about kinda conform to the Drupal concept of "multisite". That is, a single codebase that serves a variety of different sites. I figured out a pretty simple hack for this one that maybe I'll share [in another blogpost](http://www.ignoredbydinosaurs.com/2014/08/drupal-angular-tidbits-multisite) if I get around to it, but basically it involves setting a `siteVar` in the bootstrapping phase of the Angular app based off of the `window.location.hostname`. I have a `Config` Angular service that stores the mapping of hostname to siteVar. It's easy and it works.

The way we serve different stylesheets to different brands is by setting up a `sites/$BRAND/` folder that houses site specific styles, etc. When Angular bootstraps, it uses the siteVar variable to fill in `$BRAND`, and the site specific stuff is loaded. It's easy and it works. Except in Firefox.

Firefox doesn't like this setup, and particularly on the favicon, it proved to be a real PITA.

The default Yeoman favicon would always show up in Firefox, nothing else, since we had that favicon path set dynamically by Angular after the app loaded. it just wasn't responding to any of the usual hacks, and it turns out FF has a long and storied history with the favicon.

Having just found the perfect hammer for the previous problem, I thought I'd see if it could solve this one.

## Map all the things...

So for this one, I have an Nginx map companion to the one that I have in Angular.

~~~
map $http_host $sitevar {
  default "";
  ~rdmag "rd";
  ~alnmag "aln";
  ~bioscience "bt";
  ~cedmagazine "ced";
  # etc...
}
~~~

This maps the incoming host variable on the request to a $sitevar variable, used like this...

~~~
location /favicon.ico {
  try_files /sites/$sitevar/favicon.ico $uri;
}
~~~

So the browsers that respect the dynamic favicon path continue to work, and if FF never cooperates, Nginx catches that request and fixes the problem before anybody knows...