---
title: The best, least understood aspect of Drupal multisite config
date: 2015-05-13
tags: 
  - drupal
alias: 260-the-best-least-understood-aspect-of-drupal-multisite-config
---

We are currently running a Drupal multisite installation on Acquia's enterprise cloud. We have a bunch of different domains for the various sites, as well as the various environments in which they run. The development domains look like `pddah.dev.abm`, `pddah.staging.abm` etc, presumably to prevent them from being accessed from the outside world. 

This setup requires a rather voluminous sites.php file in the root of the `sites/` directory to map all the potential incoming hostnames to their correct websites. 

A simpler way around this is to make use of how Drupal maps incoming hostnames to the correct `sites/\*` folder in the first place. 

---

If there is nothing in the `sites/` folder except for default, then that is what will get loaded no matter what the incoming domain. This is Drupal's default config, in fact. If you want to go multisite, you create `sites/\*` directories for each of your websites' domains and Drupal will figure it out for you. But, it's rules for how it routes are a little bit liberal.

For example, I'm running `pddnet.com`, but the website actually exists in `www.pddnet.com`. I only have a `pddnet.com` folder in `sites/` though, so that means that any subdomain of `pddnet.com` will also route to that directory. If I create a local development domain `local.pddnet.com`, assuming my local network and apache configs are in order, Drupal will load the config out of the `pddnet.com` directory without having to do any more work or add anything to `sites.php`.

This means that you can create `dev.pddnet.com`, `staging.pddnet.com`, `whateveryouwant.pddnet.com` and provided the network plumbing is right between here and there, it'll just work. 

Of course, this also requires you having the same settings file in all of these different environments, which means that either you have to have the same DB settings in every environment, or you need to figure out some other way to load in env specific config into that file.

Acquia has a methodology that I'm probably under NDA to not divulge here, but it was devised in an era before modern PHP was a thing. These days we have tools like [phpdotenv](https://github.com/vlucas/phpdotenv), and it's that tool that I'm exploring currently for some work that we're doing here that'll span multiple environments.

When I work out how best to integrate it with Drupal, I'll let you know. So far so good though.