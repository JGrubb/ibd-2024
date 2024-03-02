---
title: Weaning off of jQuery animations with CSS Transitions
date: 2013-12-09
tags: 
  - ui
  - javascript
  - css
alias: 52-weaning-jquery-animations-css-transitions
---

I've recently finished up a project here at the job that gave me a blank check as far as writing the front end code was concerned. It was among the most blissful Drupal projects I've ever worked on, as my boss did all of the Drupal stuff, and I wrote all the code. It was heaven. 

So, there were a lot of requests for some cool javascript features, and rather than reaching for the plugin drawer, I decided to write most of them from scratch. The main feature pages are mostly [like this](http://www.advantagemedia.com/brands/electronic-component-news). The left hand "scrollspy" navigation was ripped off from Twitter's Bootstrap UI library, and (imho) warrants it's own write up, as it's some of the coolest code I've written yet. 

Most of the moving features on the site, especially the left nav when activated and the contact tab flyout thingy were written initially using jQuery to animate positioning and display properties on the DOM elements themselves. "60 fps" is the battle cry of the UI engineer this month though, so I thought I'd try a few new tricks, namely swapping out those jQuery animations for CSS transitions. Turns out this is insanely easy, requires way less code than the previous alternative, and will outperform the JS implementation any day of the week.

~~~js
var ABM = window.ABM || {};
ABM.contactFlyout = {};
 
ABM.contactFlyout = (function() {
 
var $ = window.jQuery;


var cloneContacts = function() {
// Clones the contacts div, which already exists on the page,
// attaches the copy to the page elsewhere where it can be 
// persistent, and slid out with a toggle.
};
 
var attachToggle = function() {
  $flyout = $('#flyout-wrapper');
  $('#toggle', $flyout).on('click autoFlyout', function() {
    var posY = ($flyout.hasClass('open')) ? -670 : 0;
    var winWidth = $(window).width();
    $flyout.animate({
    right: posY
    }, 350);
    $flyout.toggleClass('open');
    $(this).toggleClass('open');
  });
}

var flyoutFlash = function() {
// A thing Marketing wanted where the flyout would popout if you had never
// visited this page before, determined by a cookie.
}

// positions the contact flyout
var makeSticky = function() {
// SSIA
}
 
return {
  init: function() {
    if ($('.lt-ie9').length) return;
    cloneContacts();
    attachToggle();
    setInterval(makeSticky, 250);
    var cookieRE = /flyout/;
    if (!cookieRE.test(document.cookie)) {
      flyoutFlash();
      document.cookie = 'flyout=flown';
    }
  }
}
 
})();
~~~

## The initial implementation.

So, obviously, the meat of the animation is in that attachToggle() method, which is totally poorly named and I'll refactor that right after I finish writing this. But, it just animates the positioning of that whole div, totally simple, right? So the only thing that's animating the is "right" property of that div, maybe we should try a CSS transform on that instead?

Turns out all that involves is removing that animation bit so it looks like this -- 

~~~js
var attachToggle = function() {
  $flyout = $('#flyout-wrapper');
  $('#toggle', $flyout).on('click autoFlyout', function() {
    $flyout.toggleClass('open');
    $(this).toggleClass('open');
  });
}
~~~

And then the stylesheet for that div goes from this --

~~~sass
#flyout-wrapper
  position: fixed
  width: 700px
  right: -670px
  top: 20px
  z-index: 3
~~~

To this --

~~~sass
#flyout-wrapper
  position: fixed
  width: 700px
  right: -670px
  top: 20px
  z-index: 3
  transition: right .5s
  &.open
    right: 0
~~~

Yeah. Seriously. Instead of animating whole element, try just adding a class to it - the after state - and animate the in between with CSS. I did the same thing with the left nav dropdown bit. The whole page is a lot smoother now and I'm going to rework the main nav as soon as feasible.

#### Edit

If you're animating the position of something, say a `#page-wrap` element for an off-canvas menu, and it should present in a "normal" state when not activated, you still have to specify the default positioning (ie. `left: 0`), or the transition **will not work**. Just wasted too much time figuring out why my off canvas nav wasn't working like I wanted it to.