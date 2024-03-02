---
title: Finding that pesky Acquia host entry for your development environment
date: 2013-05-21
tags: 
  - drupal
  - devops
alias: 38-finding-pesky-acquia-host-entry-your-development-environment
---

At my gig we host our sites on Acquia's dev cloud. The dev environment is pretty locked down, obviously, since you don't want multiple, publicly accessible copies of your sites floating around out there, especially when it could be in any state of brokenness at any given time. So the way we do it is to use `.dev` domains that aren't publicly routable via DNS. We have a big ole master hosts entry in the local network that takes any of those dev domains and routes them to the proper IP.

Today however, I go out and my car won't start. This means I'm working from home, and that I need to add these host entries on my local machine myself since I'm not on the company network. As I type this I realize I could VPN, but that's no fun and I'm already done with this method that I'm about to explain.

So, I go to Acquia's cloud panel thingy. They have a "servers" menu item, but it only gives you the names of your servers, not IP addresses. Oh, we also have a load balancer sitting in front of everything that I can't log in to to get the IP that way (via `ifconfig`, presumably). So, they do give you this really vague

> The following tables show all of the servers and the services(s) they provide for each of your site's environments. Each server's full DNS name is **server**.prod.hosting.acquia.com.

So anyway, the answer is the `dig` command - [more or less a DNS swiss army knife](http://en.wikipedia.org/wiki/Dig_(command)). 

`dig server.prod.hosting.acquia.com` spits back a wealth of info at you, including the IP address of your load balancer, which you can then put into an entry in your `/etc/hosts` file. 

Love this stuff...