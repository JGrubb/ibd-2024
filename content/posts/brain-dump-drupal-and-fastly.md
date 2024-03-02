---
title: Brain Dump - Drupal and Fastly
date: 2015-02-02
tags: 
  - brain dump
  - varnish
  - drupal
  - devops
alias: 252-brain-dump-drupal-and-fastly
---

#### Prelude

Fastly is a CDN (content deliver network). A CDN makes your site faster by acting as a caching layer between the world wide web and your webserver. It does this by having a globally distributed network of servers and by using some DNS trickery to make sure that when someone puts in the address of your website they're actually requesting that page from the nearest server in that network. 

~~~
$ host www.ecnmag.com
www.ecnmag.com is an alias for global.prod.fastly.net.
global.prod.fastly.net is an alias for global-ssl.fastly.net.
global-ssl.fastly.net is an alias for fallback.global-ssl.fastly.net.
fallback.global-ssl.fastly.net has address 23.235.39.184
fallback.global-ssl.fastly.net has address 199.27.76.185
~~~

If they don't have a copy of the requested page, they'll get it from your webserver and save it for the next time. Next time, they served the "cached version" which is way faster for your users and lightens the load on your webserver (since the request never even makes it to your webserver). [Excellent writeup here](https://docs.fastly.com/guides/how-fastly-services-work/intro-to-caching-and-cdns).

There are many different CDN vendors out there - Akamai being the oldest and most expensive that you may have heard of. A new entrant into the market is a company called [Fastly](http://www.fastly.com/). Fastly has decided on using Varnish as the core of their system. They have some heavyweight Varnish talent on the team and have added a few extremely cool features to "vanilla" Varnish that I'll get to in a moment.

Fastly's being built on top of Varnish is cool, mainly because every CDN out there has some sort of configuration language and to throw your hat in with any of them is also to throw your hat in with their particular configuration language. Varnish has a well known config format called VCL ([Varnish configuration language](https://www.varnish-cache.org/docs/3.0/reference/vcl.html)) which, on top of having plenty of documentation and users out there already, is also portable to other installations of Varnish so that learning it is time well spent. This is the killer Fastly feature that first drew me in. 

---

(you can skip this - backstory, not technical)

Prior to using the CDN as our front line to the rest of the internet, we'd been on a traditional "[n-tier](https://en.wikipedia.org/wiki/Multitier_architecture#Web_development_usage)" web setup. This meant that any request to one of our sites from anywhere in the world would have to travel to a single point - our load balancer in Ashburn, Virginia in this case - and then travel all the way back to wherever. In addition to this obvious global networking performance suck, we use a managed hosting vendor, so they actually own and control our load balancer. Any changes that we'd want to have made to our VCL - the front line of defense against the WWW - would have to go through a support-ticket-and-review process. This was a bottleneck in the event of DDos situations, or any change to our caching setup for any reason. 

Taking control of our caching front line was a neccessary step. This became the second killer Fastly feature once we started piloting a few of our sites on Fastly.

---

The killer-est killer feature of all has only just become clear to me. Fastly makes use of a feature called "Surrogate Keys" to improve the typical time-based expiration strategy that we'd been using for years now. They have a wonderful pair of blog posts on the topic [here](http://www.fastly.com/blog/surrogate-keys-part-1/) and [here](http://www.fastly.com/blog/surrogate-keys-part-2/). 

The way that Varnish works is basically a big, fast key-value store. How keys are generated and subsequently looked up, as well as how their values are stored are all subject to alteration by VCL, so you have a wonderful amount of control over the default methodology. By default it's essentially URLs as keys, and server responses as values, and this will get you pretty far down the line, but where you bump into the limits is as soon as you start pondering that each response has but one key that references it. Conversely, each key references only one object. _By default..._

Real life example - I work for a publishing company. Our websites are not super complicated IA-wise. We have pieces of content and listing pages of that content, organized mostly by some sort of topic. A piece of content can have any number of topics attached to it, and that piece of content (heretofore referred to as a "node") should show up on the listing pages for any one of those terms.

Out of the box, Fastly/Drupal works really well for individual nodes. Drupal has a module for Fastly that communicates with their API to purge content when it's updated, so if an editor changes something on the node they won't have to wait at all for their changes to be reflected to unauthenticated users. The same is not true for listing pages. Since these pages are collections of content and have no deeper awareness of the individual members of the collection, they function on a typical time-based expiration strategy.

My strategy for the months since we launched this across all of our sites has been to set TTLs (time to live, basically the length of time something will be cached) as high as I can until an editor complains that content isn't showing up where they want it to. I recently had an editor start riding me about this, so lowered the TTLs to values so low that I knew we weren't getting much benefit of even having caching in the first place. I'd known about this Surrogate Key feature and decided to start having a deeper look.

---

![](https://www.learnreligions.com/thmb/zwhin5mSXvaWFBV8I-SJQ5NthtA=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/GettyImages-473256450-49bf9f7241db4febbd85d8d629cc413e.jpg)

The ideal caching scenario would have not only the node purged when editors updated it, but to have listing pages purge when a piece of content is published that should show up on that listing. This is where Surrogate Keys come into play. The Surrogate-Key HTTP header is a "space delimited collection of cache keys that pertain to an object in the cache". If a purge request is sent to Fastly's API to purge "test-key", anything with "test-key" in the Surrogate-Key header should fall out of cache and be regenerated.

In essence, what this means is that you can associate an arbitrary key with more than one object in the cache. You could tag anything on the route "api/mobile" with a surrogate-key "mobile" and when you want to purge your mobile endpoints, purge them all with one call rather than having to loop through every endpoint individually. On those topic listing pages you could use the topic or topic ID as a surrogate-key, and then any time a piece of content with that topic is added or updated, you can send a purge to that topic ID and have that listing page dropped. _And only that listing page dropped_.

~~~php
// the basic algorithm, NOT functional Drupal code

if ($listing_page->type == "topic") {
	$keys = [];
	
	// Topics can have children, so fetch them.
	// pretend this returns a perfect array of topic IDs
	$topics = get_term_children($listing_page->topic);
	// Push the parent topic into the array as well.
	$topics[] = $listing_page->topic;
	
	foreach($topics as $topic) {
	$keys[] = $topic;
	}
	
	$key = implode(" ", $keys);
	add_http_header('Surrogate-Key', $key);
}
~~~

This results in a topic listing page getting a header like this - 

~~~
# the parent topic ID as well as any child topic IDs
Surrogate-Key: 3979 3980 3779
~~~

Then, upon the update or creation of a node you do something like this -

~~~php
// this would be something like hook_node_insert if you're a Drupalist
function example_node_save_subscriber($node) {
 $fastly = new Fastly(FASTLY_SERVICE_ID, API_KEY);
 foreach($node->topics as $topic_id) {
 $fastly->purgeKey($topic_id);
 }
}
~~~

This fires off a Fastly API call for each topic on that node that would cause anything with that surrogate key, aka topic ID, to be purged. This would be any topic listing page with this topic ID on it. Obviously if there are 500 topics on any piece of content you'll probably want to move this to a background job so you don't kill something, but you get the idea.

---

This is sort of like chasing the holy grail of caching. In theory this means that you are turning the caching TTLs up to maximum and only expiring something when it actually needs to be expired based on user action and intent, not based on some arbitrary time that I decide on based on my lust for having everything as fast as possible. The marvelous side effect of this is that (again in theory) everything should load even faster since there's almost no superfluous generation of pages at all.

I just released the code on Friday morning, and the editor who was previously riding me about this topic had only positive feedback for me, meaning - so far, so good.

---

FYI - the holy grail actually looks more like this -
![](/uploads/image/image/65/medium_landscape_Screen_Shot_2015-02-06_at_10.47.36_AM.png)