---
title: How to test meta tags with CasperJS
date: 2012-07-03
tags: 
  - testing
  - javascript
alias: 24-how-test-meta-tags-casperjs
---

> Hi, this post is wildly out of date. I tried to follow it to set up some test within the last year and none of this stuff actually worked. The concepts are likely still valid, but don't expect to be able to copy much.

jg - Oct 2015

---

I went down to Drupaldelphia (the name should be self-explanatory) a couple weeks ago mainly for a session called "Testing your site with CasperJS". [CasperJS](http://casperjs.org/index.html) is what's known as a "headless" Webkit testing framework. That means it's essentially a browser and can click around your site, fill out forms, test validation, etc. It's pretty much exactly what I've been looking for for one of my clients for a while now. I knew they were out there, but there's nothing like having something shown to you for an hour to really help you get your head around it.


So I came home and immediately went to work trying to figure out how to use it.


I have an assignment right now from a client to reorganize their website per instructions from their SEO vendor. It's about 10 or 12 little, tiny changes to about 50 pages. Most of the changes involve redoing their URLs, splitting content up, updating meta tags, page titles, and the like. The pattern for the updates is completely repetitive, but altogether we're talking about 500 or so changes to a Drupal site, which means it's going to be me sitting there typing all of this stuff into a web page edit form. I will screw up. I wont know it until somebody sends an email yelling at me. Enter Casper.


Casper is a Javascript tool. You can also use Coffeescript.


The tricky thing about Casper is that it basically has two scopes. One is the testing environment in which you write most of your code, and the other is the actual page environment, where the actual markup you might want to test is. Casper has a host of functions that all center around one called [Casper.evaluate()](http://casperjs.org/api.html#casper.evaluate "API documentation | CasperJS 1.0.0-RC1"). Code executed here in executed in the browser window context/scope.


I had a hell of a time trying to figure out how to test the meta tag for these pages. I knew I wanted to set up an array for the 25 different states =

A few utility functions to help out - 

The base url = `url = "http://example.com"`


Create an instance of Casper. `casper = require('casper').create()`


I'll plop the code first and explain after.



The first line `casper.start()` obviously starts the test. `each` is a method which will look familiar if you work with any modern OO languages. It iterates over an array. The array in this case is `states`. `self` is the Casper instance, and `state` is the name of the current member of the array we're testing.


Most of this is pretty standard Casper 101. The more interesting stuff is about halfway down. 



`@evaluate` drops us into the actual page context. There we can operate on what's in the DOM, as well as things that appear as a result of actions in the DOM. The contents of that function make a selection from one select box. The value of that select box determines what gets returned from a jQuery Ajax call. The return of that Ajax call populates another select box. Ajax 101, but we're testing, automatically, 25 pages here! You have to use legit javascript unless you can figure out how to get jQuery into this context. I haven't figured it out yet because actually learning core JS is something I need to do more of.


After that all happens we drop back into the DOM again to make sure that there are more than one option in the second select box. 


### Finally - Meta tags


Sorry. So I'm kinda new to JS and function scope gave me a time on this one. The main problem I was having was trying to get a piece of text that could only be found in the DOM scope back into `Casper.each()` scope so I could run some sort of `assert()` on it. Turns out it was really easy after several days of passive Googling.



So this drops back into the DOM and pulls out the contents of the Description meta tag and assigns it to the var `descrip`. Then all you have to do is a simple `assertEquals()`



Presto.