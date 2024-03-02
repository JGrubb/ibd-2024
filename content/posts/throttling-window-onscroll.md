---
title: Throttling window.onscroll
date: 2014-10-20
tags: 
  - javascript
alias: 244-throttling-window-onscroll
---

Simple trick for making sure that anything that you want to listen to `window.onscroll` doesn't eat up too many cycles while it's doing its thing. It's called "throtting".

---

Throttling basically means, if you're receiving a steady stream of input from something, you don't really want to be firing stuff off based on that steady stream. This is a performance suck. Let's say you have this ---

~~~js
window.addEventListener('scroll', function() {
  // Stuff that's actually kinda CPU intensive like
  // taking measurements, waiting for some element
  // to show up on the screen, for example. 
  console.log('hi!');
});
~~~

This function is going to be firing as many times a second as your computer can handle. If you're on a beefy laptop in Chrome, this will probably not be noticeable, but make no mistake -- none of your users are on as good a laptop as you are. You will definitely drop frames and your perceived performance will suck wind.

What's the answer? Throttle that code. Like this.

~~~js
// timeNow is the current time in milliseconds, created by
// casting new Date() to a number with +
var timeNow = +new Date();
window.addEventListener('scroll', function() {
  // if the current time in milliseconds - timeNow is
  // less than 250, abort.
  if((+new Date() - timeNow) < 250) return;
  // Else, reset timeNow to now.
  timeNow = +new Date();
  console.log('hi!');
});
~~~

This is hack-y looking because it's kind of a hack. [Underscore](http://underscorejs.org/#throttle) and [Lodash](https://lodash.com/docs#throttle) have this built in, but it might be a little heavier than what you need. If you find yourself using this more than once in a file, please either bring in Lodash, or rip off their implementation into your project.