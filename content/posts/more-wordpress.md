---
title: More on Wordpress
date: 2010-10-01
tags: 
alias: 217-more-wordpress
---

This one is for my buddy, Bob. Bob is an old school developer who has recently been baptized by the cooling waters of the web. He's building a site in Wordpress, so I thought I'd post a few relevant pieces of info up here for him, and those of his ilk.


First of all, realize that Posts and Pages are almost the exact same thing. The key difference between the two as far as I can tell is not that Pages are easier to add to the top-level navigation, but that Posts allow you to classify them with "Categories". This allows Wordpress to work it's relational database magic, and in the hands of a skilled template author you can really take a very simple system and make it produce just about any listing of content that you want. When in the early stages of developing a site, it's better to take what you'd think of as top-level content items and see if there's any way that you can make it a listing of Posts in a certain Category, rather than just dumping related info on one Page. The chief advantages of working this way are maintenance and search-ability.


The [Wordpress Codex](http://codex.wordpress.org/) is a wonderfully organized place to learn a lot about authoring templates. Most of the Wordpress core is basically just a large group of functions for talking to a database, and much of the magic of pulling the info back out in the way that you want it is in the template layer. Thus, the [Theme Development](http://codex.wordpress.org/Developer_Documentation) section of the Codex is the place to spend a couple of days getting used to the way Wordpress works.


I'd suggest downloading my very [bare bones base theme](http://github.com/JGrubb/Base-WP-theme) and stepping through some of the template files while browsing the [Template Tags function reference](http://codex.wordpress.org/Template_Tags). It may not make sense immediately, but it won't take long.


