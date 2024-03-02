---
title: Building a drop down menu from scratch
date: 2014-02-13
tags: 
  - ui
  - css
  - javascript
  - theory
alias: 222-building-a-drop-down-menu-from-scratch
---
Dropdown menus may seem like something that falls under the "solved problem" category, but they can be surprisingly tricky. Tutorials that you find online will usually walk you through a very simple example that assumes markup that you never have. This will not be that. We're going to talk about the theory behind building a drop down so that you can better reason your way through the mess of markup that you're given. 

If you're working with Drupal and your requirements are outside the scope of what [Nice Menus](https://drupal.org/project/nice_menus) can give you (which happens as soon as you hear the word "responsive"), this tutorial is for you.

> [!note]
> Be advised that some parent themes do not render the submenus even if you set the parents to "expanded" in the menu admin. I'm not sure what the logic is for that, but it's a feature you should be aware of in some base themes.

---

#### Beginning

Your menu markup is going to look something like this --

~~~html
* Item 1
* Item 2
* Item 3
	+ Sub-item 1
	+ Sub-item 2
	+ Sub-item 3
* Last item
~~~

Out of the box that'll render something like this

- Item 1
- Item 2
- Item 3
	- Sub-item 1
	- Sub-item 2
	- Sub-item 3
- Last item

If you are working with Drupal, you're going to have to dig through a lot of layers of wrapper divs, and there will be a lot more classes added to each item, but the general structure is the same. One early gotcha is that all the submenu `ul`s are also given a class of `.menu`, which is annoying at best.

~~~html
* Item 1
* Item 2
* Item 3
	+ Sub-item 1
	+ Sub-item 2
	+ Sub-item 3
* Last item
~~~

Ignoring all that, the general idea is to hide the submenu ul, get all the top level items to line up next to each other, and show the submenus when you hover over a parent item. How?

~~~css
ul {
	float: left;
	margin: 0;
	padding: 0;
}
ul li {
	position: relative;
	float: left;
	list-style: none;
	list-style-image: none; /\* DRUPAL!! \*/
	padding: .5em 1em;
	margin: 0;
}
ul li ul {
	display: none;
	position: absolute;
	width: 10em;
	left: 0;
	top: 1em;
}

ul li:hover ul {
	display: block;
}
~~~

[JSBin here](http://jsbin.com/cemuk/5/edit?html,css,output)

#### Play by play

I'll assume that the left floating stuff is understandable. The real action with a drop down happens with the `display:none; position:absolute` set on the submenus and the `position:relative` set on the parent `- `s. `position: relative` means nothing much to the item on which it's set (unless you start adding positioning rules to it as well). It's importance here is because any child elements/nodes that are absolutely positioned inside of it will now be positioned as if they exist inside that element. Without `position:relative` on that item, the absolutely positioned elements inside of it will position themselves relative to the body element, or the first containing ancestor that is positioned relatively. [See here for an example](http://jsbin.com/cemuk/2/edit?html,css,output). 
 
As an aside, these two ALA articles are required reading if this part makes your eyes cross.

- [CSS Positioning 101](http://alistapart.com/article/css-positioning-101)
- [CSS Floats 101](http://alistapart.com/article/css-floats-101)

The rest of this is hopefully understandable. `display: none` on the submenu hides it from view, until you hover over it's parent `- `, at which point it gets the display property of `block`, which makes it show up in your browser. Since it's absolutely positioned, it'll need a width specified. You'll need something narrow enough to prevent short item from floating next to each other, but wide enough to keep longer items from breaking to too many lines.

#### On Superfish, Nice Menus, javascript, etc

Presumably, you might have heard of Superfish. It was the defacto JS solution to drop downs for many years, most of them in IE6/pre-jQuery era. IE6 has a (ahem) "feature" where only certain elements properly respond to the `:hover` pseudo-selector. That meant for a great many years that the only real solution was to patch this behavior with javascript. Fortunately, you only have to deal with this issue now if you still support IE6. 

The other, definitely legitimate issue, is that using CSS only means that the instant you leave the zone of the parent item (don't forget the the parent `-` is wrapped around the entire submenu), your submenu will disappear. This means either judicious placement of your submenu, or utilizing some javascript to make your menu behave a bit more smoothly. Both are good solutions, imo.

[Here's an updated JSBin](http://jsbin.com/cemuk/7/edit?html,js,output). Note in the collapsed CSS column I've commented out this part --

~~~css
ul li:hover ul {
	/*display: block;*/
}
~~~

	
This means we'll be hiding and showing the dropdown with javascript (jQuery in this example). I've added a class of `expanded` to the parent `* ` to make selector targeting easier. Here's the full javascript -
	
~~~js
jQuery(function($){
	var timerOut;

	$('.expanded').on('mouseover', function(){
		clearTimeout(timerOut);
		var self = this;
		setTimeout(function(){
		$('ul', self).show();
	}, 300);

	}).on('mouseout', function() {
		var self = this;
		timerOut = setTimeout(function() {
		$('ul', self).hide();
	}, 300);
	});
});
~~~

So, [setTimeout returns a numeric timer id](https://developer.mozilla.org/en-US/docs/Web/API/Window.setTimeout) that you can use to cancel out the setTimeout callback if you need to. Since we're going to need access to one event handler's timeout in another event handler, we're going to declare the variable for the timerId outside the scope of both of them - `var timerOut` in the outer function. 

Any time you use [jQuery `on()`](http://api.jquery.com/on/), the element that is triggering the handler is `this` inside the callback function (the function after 'mouseover'). We'll assign that to `var self;` since we're going to enter another context once we enter the `setTimeout()` callback. By the way, all of this gobbledygook about scope and `this` is THE trick to Javascript. Understand function scope in Javascript and you'll be highly paid. I'm still getting there myself.

So anyway, discounting that bit, it's very simple. When you mouseover the parent, show the submenu. When you mouseout of the parent, hide the submenu. All we're doing is adding a delay to those actions firing. The trick is cancelling that `hide()` call if the user decides within 300ms that they didn't mean to wander out of the submenu. That's where `clearTimout()` works it's magic in the mouseover function. If there is a mouseout timer still ticking, it's ID will be assigned to `timerOut` and it'll get cleared. If it's already hidden the submenu, no harm and no foul. 

Note that if `$('ul', self)` looks weird, what that means is the `
` item in the context of `self` is what we're trying to find. Omit the context and it implicitly becomes the whole window. Add the context and is almost the same as saying `$('li.expanded ul')`. I say "almost" because the second, longer example will actually grab \*any\* `ul` inside of \*any\* `li.expanded`, which is not what you want. That's why specifying the context not only shortens your code and improves performance since the whole DOM doesn't need to be searched each time, but also scopes your selector dynamically based on which element triggered the handler. I know this is total babble, and I'm sorry.

#### Final gotcha

Drupal's version of jQuery is so dated that `on()` isn't available. If you have the option of jQuery_updating to 1.7, you can enjoy the modern era. If your site breaks, as is often the case, and you're stuck with lt 1.7, you'll need to use [`bind()`](http://api.jquery.com/bind/) instead. It works more or less the same in these use cases, but being [familiar with event delegation](http://code.tutsplus.com/tutorials/quick-tip-javascript-event-delegation-in-4-minutes--net-8961) is another JS Jedi trick, and the one promoted by modern Javascript authors.

#### In closing

This got longer than I wanted, but it's not the easiest thing in the world to build the ubiquitous drop down menu. My first one took me at least a week, and I think I eventually stumbled on Nice Menus to actually get the job done. Luckily, modern browser environments are much more predictable than they used to be, so knowing how to fish on your own is much easier these days, and the taste of a fish you caught on your own is always superior to something bought at the store, right?

This post touched on the word "responsive" at the top, and I'll follow up with how to work with that. If you've come this far, you've set yourself up nicely for an easier mobile menu job without having to fight against a bunch of other people's code.
