---
title: Tracking Pixels 101
date: 2014-10-04
tags: 
  - javascript
  - analytics
alias: 242-tracking-pixels-101
---

So tracking pixels. They sound awful. They sort of are, but we all use them. One just fired off on you a minute ago when you loaded this page. That's how Google Analytics works its magic. But how do they work? [The GA tracking code is Javascript](posts/deconstructing-the-google-analytics-tag) and doesn't say anything about an image pixel.

Step inside...

---

## Dat JS

So that javascript does a few things, primarily it creates another javascript tag that pulls down the real "payload", which is a hackerish term for "a bigger ball of code". I haven't analyzed that code yet, but one of the things that it does is build a profile of your browser that you're on and the page that you're looking at. Once it does that it pings GA's tracking servers with that profile which counts as a "pageview". That's the `ga('send', 'pageview')` bit. But how does that work?

A tracking pixel!

## Placement in the DOM, you need not...

So a pretty interesting thing about tracking pixels, and anything in the browser really is that it doesn't actually need to be put on the page to exist in memory somewhere. In fact, even if that pixel is only 1x1 in size, it could bump something out of the way enough to trigger a repaint of the webpage, which might alert you to that pixel's existence, which is something that advertisers and their ilk stringently avoid.

So basically, that `ga('send', 'pageview')` ends up generating a request to a server somewhere. That request looks like this 

~~~
https://www.google-analytics.com/collect?v=1&_v=j29&a=806595983&t=pageview&_s=1&dl=http%3A%2F%2Fwww.ignoredbydinosaurs.com%2F2014%2F09%2Fdeconstructing-the-google-analytics-tag&ul=en-us&de=UTF-8&dt=Deconstructing%20the%20Google%20Analytics%20tag%20%7C%20Ignored%20by%20Dinosaurs&sd=24-bit&sr=1440x900&vp=1334x479&je=1&fl=15.0%20r0&_u=MACAAAQAI~&jid=&cid=1626931523.1412365384&tid=UA-8646459-1&z=962163205
~~~

In the network tab of your devTools in your favorite browser you can break down all those query string params into something a little more interesting.

~~~
v:1
_v:j29
a:806595983
t:pageview
_s:1
dl:http://www.ignoredbydinosaurs.com/2014/09/deconstructing-the-google-analytics-tag
ul:en-us
de:UTF-8
dt:Deconstructing the Google Analytics tag | Ignored by Dinosaurs
sd:24-bit
sr:1440x900
vp:1334x479
je:1
fl:15.0 r0
_u:MACAAAQAI~
jid:
cid:1626931523.1412365384
tid:UA-8646459-1
z:962163205
~~~

Some of that stuff is understandable, some of it is not. But the point is that that request actually trigger a response of a 1x1 pixel. 

~~~
# Response headers

access-control-allow-origin:\*
age:66666
alternate-protocol:443:quic,p=0.01
cache-control:private, no-cache, no-cache=Set-Cookie, proxy-revalidate
content-length:35
content-type:image/gif
date:Fri, 03 Oct 2014 18:23:52 GMT
expires:Mon, 07 Aug 1995 23:30:00 GMT
last-modified:Sun, 17 May 1998 03:00:00 GMT
pragma:no-cache
server:Golfe2
status:200 OK
version:HTTP/1.1
x-content-type-options:nosniff
~~~

If this were the first time I'd visited the internet, there would almost certainly be a set-cookie header in there as well, but since they set that cookie on me a LONG time ago, it doesn't get sent. 

The kinda creepy thing is that since Google Analytics is on a large number of sites, and their origin servers are on the same domain (cookies), they can follow you around the internet from site to site to site in a way that nobody else can (save perhaps the other giant analytics providers, which probably have nowhere near the reach, unless you count Facebook).

---

## Wow, cool. So what?

So that image pixel is not really the point. It gets returned in that response, but doesn't get put on the page. It plants a cookie on you, big deal.

But what happens at Google is that the request that was made in the first place gets logged. It gets broken down by its query string params, and that's how they build the tool. That's how you know what size browser people are on, what part of the world they're from, what they looked at, and what they clicked on (if you're tracking events).

The really interesting part to me, and the part I haven't figured out yet, is how they store ALL that data on the backend to build the reports out of. Think about it -- they're basically logging every request made to every website that is running their code. That's a really big number, even for Google. And then they're able to pull your report suite out of all that data, and sort it out by whatever you wanna know. Seems pretty cool, and also well beyond the capability of normal relational DBs.

A post in the future, I imagine....