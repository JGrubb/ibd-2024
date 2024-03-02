---
title: Drupal 101
date: 2011-03-17
tags: 
  - drupal
alias: 163-drupal-101
---

I've recently begun a new contract for a rather large Drupal site.  I was very excited to land this contract as it shall be my first bona fide "enterprise" contract and there's already a large amount of content on the site despite the company being rather young.  There are a *lot* of forms on this site with leads being transferred to a backend system that I'll probably never have anything to do with.  Anyway, I was excited to get a look in the back end of the Drupal portion of the site to see what the previous developers had been cooking up.

Imagine my intense surprise when it quickly became apparent that the previous developers weren't very learned in the *Drupal way*.  Specifically, they committed some major Drupal rookie sins that a novice Drupal developer must know about.

1. **Modules don't belong in the modules directory.  Themes don't belong in the themes directory.**

There are many aspects of Drupal that are, politely speaking, counterintuitive to the new developer.  Drupal by itself doesn't actually do a whole lot.  The concept of the "lean core" means that the basic Drupal functionality is expected to be augmented to by [community contributed modules](http://drupal.org/project/modules).  You'd be hard pressed to find a Drupal site out in the wild without at least half of those front page modules installed.  I learned this on my second or third week of working with Drupal - the modules directory is reserved for Drupal core modules.  All community or "contrib" modules go into a directory that doesn't even exists with a stock Drupal install - sites/all/modules.  The same goes with themes - sites/all/themes.

When Drupal runs through it's "bootstrapping" process (a fancy name for when a request from someone's browser hits anywhere on a Drupal site), it looks in a number of different places to find code that may extend it's functionality or alter it's output to a request.  Obviously, the modules directory is one of those places, that's why Drupal will run if you put contrib modules there.  However, a big part of maintaining a Drupal site involves keeping on top of updates to those modules, not to mention updates to the Drupal core itself.  Since the majority of Drupal lives in those modules, when Drupal core receives an update it becomes a much more muddlesome process to separate which files belong in which folder if they're all mixed together.  More advance development tools like [Drush](http://drupal.org/project/drush) make updating sites a total breeze, but not if the site structure is laid out wrong.

**2. Use an admin theme.**

Garland is a lovely theme (cough), but luckily the ecosystem has led to the evolution of some really useful admin themes that have big forms, big buttons and an intuitive layout by default.  My personal favorite is [Rubik](http://drupal.org/project/rubik), which requires also downloading [Tao](http://drupal.org/project/tao) to function.

**3. Speaking of updates, stay on top of them.**

Part of the wonder of open source software is the knowledge that all over the world, thousands of developers with vastly differing skill sets and experiences are working together on one project.  Such a model ensures that many sets of eyes are on the lookout for security holes ("exploits") and that many hands are at work moving the project and it's features forward.  The flipside to this rapid development pace is that updates to the software are released whenever they are ready, and this is often quite frequently on larger projects.  It's a rare week when I don't have any sites that have some sort of update released on at least one module.  Drupal maintenance is all about keeping on top of the Status Reports page - found under admin/reports/status and admin/reports/updates.