---
title: Drupal is uncool because
date: 2016-03-20
tags: 
  - drupal
alias: 273-drupal-uncool-because
---

So before the pitchforks come out, this is where this blog post came from.


> I need someone to submit “Why is drupal uncool?" to <https://t.co/WkZUojaBkS> [#DrupalCon](https://twitter.com/hashtag/DrupalCon?src=hash)
> 
> — Cathy YesCT (@YesCT) [February 22, 2016](https://twitter.com/YesCT/status/701845676213276672)




YesCT started riffing on great ideas for DrupalCon session. Alas I didn't submit any of them, I submitted my session about how Paragraphs module got my mojo back. It was not accepted, which is kind of a relief. 

Anyway, I have a few ideas on this topic. Let's see what falls out.

---

### It's PHP

So yes, PHP. AFAIK in all of it's 20+ year history PHP has never ever been the Hot New Thing. It has things to recommend it, namely the deployment story which enables folks to get started building things immediately and not have to worry about setting up anything on a server. If there were any other option that were this simple, PHP probably would not be the thing that it is today, but for that horse a kingdom has been built.

Just Google "why PHP sucks" for more on this topic. In a nutshell it's maddeningly inconsistent. I've been working with Python for all of 4 weeks and I already know it better than I know PHP in 8 years of working with it. "The API is consistent", which is a fancy way of saying that you don't have to look up the order of the arguments for a function every single time you want to use that function. Quick, `array_push()` - is it haystack/needle or needle/haystack? 

Drupal suffers from this spillover, as do basically all PHP frameworks. Until D8, Drupal didn't even have the benefit of object orientation (by benefit I mean, it makes it cooler).

### It (historically) tries way too hard

Remember this - [http://certifiedtorock.com/](http://certifiedtorock.com/)? Sadly, this is still a thing on the internet.

Or what about this one? 

![webchick world tour](https://groups.drupal.org/files/LADrupalWebchick.jpg "Webchick World Tour, 2011")

I'm super, duper sorry for dredging this up, because I have nothing but respect for Webchick. Seems like everything she writes is so dead on and the respect that she has in the community probably surpasses what even Dries gets. But I'm not sure who thought this was a good idea.

In this regard, Drupal is like me at 15 or so. I was not cool, but I went and bought Doc Martins anyway because the cool kids wore em. I think both of these are around the same era - the time that Rails was absolutely the White Hottest New Thing and everyone else was trying to keep up.

### The documentation sucks

And this is the real point here. I started working with Django last week. [The documentation is complete, organized, and located in one indexed portion of the website](https://docs.djangoproject.com/en/1.9/contents/). You can download a PDF of the entire thing and it's better than any O'Reilly book you could possibly buy about Django. If you land on a page for an old version of the framework, [it lets you know](https://docs.djangoproject.com/en/1.7/contents/).

[The same thing goes for Postgres](http://www.postgresql.org/docs/9.5/interactive/index.html).

[The same thing goes for Symfony](https://symfony.com/doc/current/book/index.html).

[The same thing goes for Rails](http://guides.rubyonrails.org/).

[The same thing goes for React](https://facebook.github.io/react/docs/getting-started.html).

These are open source projects that want to be used. They put forth as much effort into the documentation for the things they've built as they put into the things themselves. It's impossible as a Drupal dev to see documentation like this and not feel like "wow, this is a toolkit that takes itself seriously and respects folks who want to use it". It's impossible as a Drupal dev to see documentation like this and not feel like "WTF is Acquia doing?"

Drupal 8 will not succeed until it has documentation like this. I have contributed to the Drupal 8 User Guide, but good luck finding it. You can find [the project page](https://www.drupal.org/project/user_guide), but the actual documentation is nowhere on the front page results of that search. 

[Rather large rant about the JS framework in core discussion redacted.]