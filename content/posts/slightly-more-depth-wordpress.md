---
title: Slightly more in depth with Wordpress
date: 2010-10-02
tags: 
  - general development
alias: 151-slightly-more-depth-wordpress
---


> I am having a blast. I found a nice photo gallery Gen Next - has lots of cool features; still working on it.
> 
> I do need some help:
> 
> 1) how do I keep the 'home' page of the WP site clear of posts? I just want to put some html text and links. It seems to always show the latest posts. I need to understand what is going on with that page.
> 
> 2) How can I order the posts within a category. example: For Lyrics I have category per album with a post for each song. I need to order them in track order, it seems to always put the newest post first. Ordering by permalink would work since I put the track number as the 1st part of the name.
> 
> 3) Gotta turn off the right nav stuff - not needed.
> 
> I tried creating a category called Lyrics and then adding sub-categories for each album. That created the Lyrics page with all songs listed, I wanted just links to the sub-categories; plus it made the menu real ugly with all the albums listed below main menu. I resolved this by making Lyrics a page and putting links to the 'album' categories in the html. It seems to work fine but I am not sure if I could have done this another way.
> 
> also: where do I comment out the 'add comments' stuff?
> 


Alright, in order --


1. Go to Settings -> Reading and set the top option to display a static page as the home page. You'll have a list of options for which static page you want.
2. As with almost any programming challenge, there are a couple of ways to go about it. I'm not exactly sure how you want them to display in every situation, but I'll take a beginning guess.
	1. The easiest, and in this case probably best way to go is to just make sure you enter the lyrics for the songs in the reverse order than how you want them to appear. If you've already entered some of them in, you can alter the "published date" on the edit screen of each lyric page. I'd just edit that have them line up in the order that you want.
	2. The more stylish option would be to write a function in your theme's `functions.php` file. To make this work correctly you'd have to make use of the "custom fields" feature. I'm a tiny bit rusty on the exact way to go about this, but basically "custom fields" are extra bits of metadata that you can add to any page or post in WP. For instance, you could create one custom field with a key called "track number" and use the value of that field to define the track number. You'd then figure out the correct function to call with the correct arguments to make it show up in the right order when someone came to the lyrics section of the site.
	3. Doing the same thing from the theme template would also be an option, and probably a little more realistic since you'd be able to have the correct template called by following the naming convention for a given category. [This page](http://codex.wordpress.org/Template_Hierarchy) will tell you how.
	4. Slightly off-topic, but there's also the option of creating a blanket "Lyrics" category, and then creating a post for each album that simply lists the songs in the running order, and then links the song titles to another post that contains the lyrics for that song. This option might give you the most control for the least effort.
3. So you don't need a right sidebar at all?
4. Right, yeah I would ditch the sub-categories for the albums, and probably go with option 4 above.
5. If you look at [line 29 on the page.php](http://github.com/JGrubb/Base-WP-theme/blob/master//page.php) file and [line 39 on the single.php](http://github.com/JGrubb/Base-WP-theme/blob/master//single.php) file, you'll see the comment function. I'd just comment it out so it reads -

`<?php //comments_template( '', true ); ?>`  

and your comments will be gone.


