---
title: Drupal Paragraphs - what is structured content?
date: 2016-04-26
tags: 
  - workflow
  - drupal
alias: 282-drupal-paragraphs-what-is-structured-content
---

So I gave [a presentation at Drupaldelphia](http://drupaldelphia.org/session/how-structured-content-and-paragraphs-module-reignited-my-drupal-flame) a few weeks ago about the [Paragraphs module](https://www.drupal.org/project/paragraphs). 

The Paragraphs module is my favorite Drupal module that I've come across in probably the last 5 years. It's basically Drupal's implementation of the concept of "structured content" - one of those terms that sounds so abstract that you probably feel an unconscious repulsion to even learning more about the idea, but hopefully I can help get you over that.

---

### The problem

The problem is the dreaded \*body field\*. The body field is (historically) basically the dumping ground for everything that is going into a piece of content on the website. For sites like this blog, made up of 99.8% text, it works fabulously well and I suspect that in the early days of blogging and the internet most content that went into some kind of CMS was modeled in this way. You're reading a body field right now. There were undoubtedly some images placed in with the text, but anything really fancy or custom was most likely coded by hand, outside of the CMS. 

Things went this way for a number of years and as CMSs like Drupal and Wordpress continued to gain popularity and more and more people began to use them to run their websites, more and more "things" began to wander into the body field. I'd very much like to add some images to this post, for example, but it's actually kind of a PITA to do it in a reliable way. 

One day some dudes invented a website where the whole world could post and share videos, then they let you embed those videos into other web pages. So now the body field has to accommodate text, images, and video embeds.

The slideshow was born. "Why can't I put a slideshow into my article?!" became a battle cry from legions of downtrodden District 12 editors. "Imgur lets me create slideshows!"

"Data journalism" comes along, and with it a thousand fancy infographics from your internal production teams and 3rd party tools alike, distributed via iframes and js snippets and holy shit letting our users embed javascript is suicide, right??

The Twitter card embed. I'll stop there.

Soundcloud. Every other media site with their own custom video player. Imgur. Flickr. Hubspot. Disqus.

### Some (crappy) solutions

This is a problem for a number of reasons. The most immediate issue that this causes is that unless all your editors know how to write perfect HTML, you're going to be stuck with The Wysiwyg. Wysiwygs have come a pretty long way in the last couple years (a few of them anyway), but I don't know of any serious Wysiwyg solution out there that is able to keep pace with the number of new "things" showing up on the internet. Our editors want to put these things in their content in a way that will effectively keep them from breaking the site, and it's our job to give that to them somehow.

The most evolved solution to this problem is the one that Wordpress came up with, and Jeff Eaton espoused last year in his DrupalCon Talk "[The Battle for the Body Field](https://www.youtube.com/watch?v=hAIM1eEw-yU)", basically - shortcodes. This approach allows for a lot of editor creativity which should be a primary goal of our solution, but puts some guardrails up so that we're not constantly fielding tickets about a broken article.

So to recap, here are the most commonly employed solutions to this problem

- Don't let them put anything in there (Markdown)
- Let them put everything in there (HTML)
- Let them put almost anything in there, but try and keep them from blowing our leg off (shortcodes)

### And yet

None of these addresses a fundamental thing that we should care about - reuse. Once you put something in the body field, it's essentially in the content roach motel, and it's never checking out. Your system can't have any awareness of what's inside that field, so unless someone manages to get to exactly that article where you used that image or that tweet, it's never going to be seen again.

There is another way though. Imagine being able to create a feed of images that were used in articles on your site that day. Imagine being able to grab all the twitter cards that were used in articles that were tagged to Cats. Or being able to easily add rich, multi-field captions to images without having to bend over backwards.

### Structured Content

So if you take a step back and think about it, a piece of content on your website is often a fairly unstructured piece of work, but it can be broken down into a collection of pieces that are themselves very structured.

Take an image with caption. Trying to do this in the Wysiwyg frequently involves adding the caption to either the title or alt attribute and then using javascript to pull that out, build a DOM element out of it, and insert it somewhere in the vicinity of the image. What happens if you also need an attribution field in addition to the caption, though? That's the instant things start getting weird, and often we give the editor some unsatisfactory answer and they slink off to solve the issue in some unsatisfactory way.

But really, what if we treat that image/caption as it's own entity? Then you have an entity with an image field and a caption field. If you want to add an attribution field, that's very easy in this model - you just add an attribution field. Or a URL. Or a date.

Something with a few more moving parts - how about that image gallery? Well, another entity for starters, but make it so you can add any number of images to the entity and presto. Since our system is aware of the kind of entity that you're using here, it's trivial to wrap it in the CSS classes needed to pull off an image gallery.

So essentially, rather than your content being something like Title/Summary/Body/Image for this/Image for that you end up with something more like Title/Summary/Collection of individual entities that make up the body of the article. Those individual entities are pretty easy to manage in themselves, since they're highly predictable. You just need some mechanism for relating them into the article that they live in and making sure they display in the right order. Once you do that though, you're not bound strictly by the article model anymore. You can use those entities in other ways as well.

This article got waaaay longer than I intended, so I'll get into Drupal's answer to this issue in the next one. As far as I know, this concept has existed in the CMS world for a very long time, but Drupal is the only platform that I know of that actually has an implementation of this concept in the Paragraphs module. Until then.