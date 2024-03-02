---
title: Omniture - not the best 3rd party javascript
date: 2014-10-24
tags: 
  - javascript
alias: 245-omniture-sucks
---

Hoping to land a slot in the SERPs with that title, I'm here to demonstrate today the difference between **good** 3rd party javascript, and **bad** 3rd party javascript.

First - the good, as demonstrated by Google Analytics. GA's setup and tracking code, [as stepped through here](posts/deconstructing-the-google-analytics-tag).

~~~js
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1\*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');
~~~

The `ga()` function is what does the magic, but in essence all that the `ga()` does is push its arguments into an array. All of this functionality is contained within those 4 lines. Those 4 lines also create a script tag that loads the rest of the GA lib, where the functionality to rifle through that array and report its contents back to GA's servers lives.

_If that script tag fails to return, nothing happens_. More importantly, **nothing bad happens**. The array that `ga()` pushes into is a vanilla JS array, it will always be there. If the GA payload doesn't come back, no worries. Your app or website will never know the difference. 

This is how knowledgable developers build 3rd party libs that don't suck.

In contrast, here's what Adobe's Omniture does.

~~~js
// A blob of obfuscated js hundreds and hundreds of
// lines long here. The essence of worst practices.
// All of this results in (hopefully) their lib coming
// down via a generated script tag as well. This script
// tag creates a global object - s (so artfully and helpfully
// named) - that has a giant range of methods to deal with 
// the functionality of their platform - pageviews, events, etc
~~~

Later, you track events and the like via a function call like this - 

~~~js
s.tl(this, 'o', 'blah'); // event
s.t() // pageview
~~~

What happens though, if their huge lib doesn't come back for some reason? Like, maybe a corporate firewall doesn't feel like letting Omniture code track its users? This would result in there being no global `s` object on which to call these methods. What happens then?

**Your website blows up, that's what.** Exceptions are thrown, and god help you if you're running a single page app, because it's toast now. 

The only solution I've been able to come up with is to wrap everything in `try {} catch {}`, which is hideous and wasteful and prone to error when you forget to wrap every single piece of Omniture code with it.

That's why I have the utmost respect for the engineering of GA, and the utmost disdain for Omniture. Oh, and Omniture costs tons of money to use, and the API documentation is buried somewhere in the 5th level of Dante's hell.