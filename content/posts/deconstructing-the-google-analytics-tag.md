---
title: Deconstructing the Google Analytics tag
date: 2014-09-03
tags: 
  - javascript
  - analytics
alias: 239-deconstructing-the-google-analytics-tag
---

If you're a web developer, I'm sure you've placed this snippet of code more into more than a few projects.

~~~js
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1\*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');
~~~

Let's unpack it a little bit -

~~~js
(function(i, s, o, g, r, a, m) {
 i['GoogleAnalyticsObject'] = r;
 i[r] = i[r] || function() {
 (i[r].q = i[r].q || []).push(arguments)
 }, i[r].l = 1 \* new Date();
 a = s.createElement(o),
 m = s.getElementsByTagName(o)[0];
 a.async = 1;
 a.src = g;
 m.parentNode.insertBefore(a, m)
})(window, document, 'script', '//www.google-analytics.com/analytics.js', 'ga');
~~~

Now let's make those local variables a little more clear -

~~~js
(function() {
	var a, m;
	window['GoogleAnalyticsObject'] = 'ga';
	window['ga'] = window['ga'] || function() {
	(window['ga'].q = window['ga'].q || []).push(arguments)
	}, window['ga'].l = 1 \* new Date();
	a = document.createElement('script'),
	m = document.getElementsByTagName('script')[0];
	a.async = 1;
	a.src = '//www.google-analytics.com/analytics.js';
	m.parentNode.insertBefore(a, m)
})()
~~~

So if you go to your javascript console and type "GoogleAnalyticsObject", you'll get back the string "ga". `window.ga` is a function, but since functions in javascript are also objects, it has a property called `q`, which is just an array. This is reminiscent of the old ga.js syntax which went something like this --

~~~js
var _gaq = _gaq || [];
 _gaq.push(['_setAccount', 'UA-XXXX-Y']);
 _gaq.push(['_trackPageview']);

 (function() {
 var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
 ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
 var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
 })();
~~~

`_gaq` is/was just a plain old Javascript array, which gives it the `push()` method for free. This new `ga.q` property serves the exact same purpose, an array to push things into and wait for something to come along later and pop them off. That something that comes along later is whatever is contained in that async script that this snippet also builds. 

This is super clever because it doesn't have to wait for anything, it can go ahead and do all its business the instant the page loads and even if the main tracking script doesn't come down for some reason, nothing breaks.

Back to analytics.js...

Whatever you hand as arguments to `ga()` gets fed into `ga.q` right here --

~~~js
window['ga'] = window['ga'] || function() {
	(window['ga'].q = window['ga'].q || []).push(arguments)
}
~~~

If you pop open the console on the front page of this blog, and type in `ga.q`, you'll get this --

~~~shell
> ga.q
[
Arguments[3]
0: "create"
1: "UA-8646459-1"
2: "ignoredbydinosaurs.com"
callee: function (){
length: 3
__proto__: Object
, 
Arguments[2]
0: "send"
1: "pageview"
callee: function (){
length: 2
__proto__: Object
]
~~~

Those are stashed in the queue because as soon as that first bit of code is parsed out, there are two quick calls to `ga()`, and that's exactly what they have as their arguments. It's so simple, it's almost stupid to explain, but the script is so heavily optimized it's not at all obvious on first glance what's going on here.

Moving on, there's another property of the `ga` function/object - `ga.l`. `ga.l` gets initialized to a javascript timestamp (in milliseconds). `new Date()` returns a javscript `Date` object, but multiplying it by the integer `1` casts it into a number, which automatically converts it into the number of milliseconds since the epoch. Another way of writing this would be `+new Date()` - another, albeit less clear way, of performing the same casting to a timestamp. `ga.l`'s purpose is to provide a time for the initial "create" and "pageview" calls to `ga()`.

Lastly, an asynchronous javascript tag is written to make the call to fetch the ga.js script from Google's servers so the real magic can start.

Another interesting bit is that the `a` and `m` parameters are not assigned anything at the [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) call at the end. This leaves them as `undefined` in the script until they are assigned the script tags toward the end of this snippet. Another way of writing the exact same thing would be to only have `(i,s,o,g,r)` as parameters to the function, and then declaring `var a, m;` somewhere in this snippet. I'm not sure off hand if this is a memory or performance optimization or if it's just a handy way to save a couple bytes over the network, but someday I'll figure it out.

---

Thanks for sticking with me - this is one of the most common little snippets that I've probably placed in my web development career, and I'd never totally dug in to understand what exactly it does beyond writing an async script tag. The pattern of declaring a "plain old javascript array" and then pushing your "stuff" into it as a queue for working with later is an extremely common pattern in 3rd party javascript, since you want everything to be as performant as possible, and you want to make sure you don't break anything if for some reason the rest of your payload script doesn't actually load.