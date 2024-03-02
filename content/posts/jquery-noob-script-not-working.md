---
title: jQuery Noob - script not working?
date: 2012-07-10
tags: 
  - javascript
alias: 26-jquery-noob-script-not-working
---
It's simple really, but you have to know a few things about how a browser renders a page to get it. jQuery is a wonderful thing because it removes the need to know a LOT about a LOT to do with the DOM and still get amazingly cool things done - slideshows, form validation, etc. However, if you go and learn a bit more about how JavaScript works in a browser you can go beyond copying and pasting code snippets and really start getting creative. Or surgical. Or whatever you want. 

So when code comes through the wire into your browser, your browser parses it from the top down. That's the fancy, mechanical way of saying it follows the instructions from the top of the page to the bottom. Any time it encounters a piece of JavaScript, be it either directly embedded in the page source or linked to another file (from your site or somewhere else), it will take a detour into that JavaScript and execute any instructions in that script before it moves on with parsing the rest of the page. If you put a humongous blob of JavaScript at the top of the page, your site will seem slower because everybody's browser is stopping to follow/execute the JS before it moves on down into the content of your page.

That means what? Well say you have a date field on a form in your page and you want to use the [jQueryUI datepicker](http://jqueryui.com/demos/datepicker/) on it. All you then need to do is write `$('#event-date').datepicker();` and you have a datepicker. But there's a catch. If you put that code at the top of your page (in the head), or in a file that's linked to in the head of your page (using `script src`), then the browser will execute that instruction as soon as it encounters it. The datepicker won't show up. You'll pull your hair out. The reason is that the browser goes and sets a `datepicker()` on an element that it doesn't know yet exists because it hasn't gotten down into the page to actually render that yet. It'd be like giving the steps for a recipe before giving the ingredient list. Only your computer is too stupid to look down the page and read the ingredients list. It'll just say `"flour? There's no flour on my table. Moving on."` and no datepicker.

There are two solutions. 

One is to delay the execution of the `datepicker()` instruction until the page has been rendered/parsed/whatever you want to call it. You do this by putting all of your custom code inside a call to `jQuery(document).ready()` like this - 

~~~js
jQuery(document).ready(function($) {
 $('#event_date').datepicker();
}

// equivalent (and better) ->

jQuery(function($) {
 $('#event_date').datepicker();
}
~~~

That basically "hides" the instruction inside of a function that doesn't get called until the page is ready. When the page is "ready" (that happens once the browser has loaded the whole page and knows what's there) the function fires and your browser gets the instruction to add datepicker functionality to the field that it knows exists now. You can put as many things inside that function as you want, all your code if you like. And you should learn more about JS because it's a wonderfully cool language.

The other solution is to put all of your JS code at the bottom of the page. This was in vogue over the last couple years as a good way to increase the perceived performance of your website anyway, but I bet lots of people were still wrapping their jQuery code in `document.ready()` even though they didn't need to. I was anyway. Since your JS isn't encountered by the browser until after the rest of the page you don't need to wrap it in that call if you put it at the bottom.