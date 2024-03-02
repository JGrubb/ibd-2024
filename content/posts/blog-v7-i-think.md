---
title: Blog v7, I think
date: 2012-08-04
tags: 
  - random
alias: 121-blog-v7-i-think
---

### v0 - Blogger.

I think I got 3 or 4 posts in before realizing I wanted to try actually throwing up a website of my own.


### v1 - [Wordpress](http://wordpress.org/)


One click installer at GoDaddy, my webhost for the first three months. 


### v2 - [Drupal](http://drupal.org/)


I wanted to get more involved in the development side of it and was really loving the opportunities in the Drupal job market. Migrating a Wordpress blog was so predictable that there was a Drupal module to do so, and it brought all of my old posts, categories, everything. The setup of the categories was a little weird - it dumped everything into a Drupal Taxonomy called "Wordpress Categories". I guess whomever wrote the module was tired of writing the module by the time they got to that point.


### v3 - Back to Wordpress


Not really sure why. I have a vague recollection of blogging in Drupal just not feeling as fun, so I didn't do it as much. I'd moved into [MarsEdit](http://www.red-sweater.com/marsedit/) because it felt more "hacker-y", but it didn't provide as fertile-seeming an environment as good ole Wordpress.


### v4 - Back to Drupal


I was serious about Drupal now. I was starting to "get" Views and needed to put a portfolio together since I'd quit RRE by that point. 


### v5 - [Jekyll](http://jekyllrb.com/)


I was starting to get braver, even though this is the dead simplest tool out there. Some other soul put together a Ruby script that'd pull all my posts out of the Drupal DB and turn them into Markdown, the format that Jekyll required. Still, blogging just didn't seem quite as fun. I guess I didn't realize how much time I had in that tour bus after all.


### v6 - Back to Drupal


I was pretty much a full time Drupalist by this point and put together a pretty decent portfolio site with it in a day or so. I was working on my [Sass library](https://github.com/JGrubb/Sass-Base-Lib) and put together a "responsive" grid in a few hours. Good times. Lasted me a while but then the itch came again.


### v7 - [Rails](http://rubyonrails.org/)


Finally came back to Rails about two months ago and rewrote this entire blog in about 6 hours from scratch. Not the trickiest of endeavors, but it took me 4 years to do that 6 hour rewrite. Wrote a Ruby script with [Sequel](http://sequel.rubyforge.org/) to pull the posts out of the Drupal DB and put them into my Rails DB, URL aliases and all. The migration script took way longer than the actual build. Drupal likes to "normalize the fuck out of your data", which means it scatters things all over the place in an attempt to store them in only one place. Did you know that the name of the column in the URL alias table is "src", whereas everywhere else in the Drupal DB it's "source". Shit like that, Drupal... I was able to put together with 4 MySQL tables what took Drupal something like 75 tables to do. Of course, the vast majority of those DB tables were never touched, which is a whole other thing about Drupal.


### v7.1 - current - Rails and [Mongo](http://www.mongodb.org/)


I've been playing with Mongo for the last couple weeks and figured "fuck it" and did it yesterday. Took me 20 minutes to migrate the posts out of MySQL and into Mongo and replace ActiveRecord with MongoMapper. 20 minutes. It took me the rest of the night to get sessions and login working again since I dumped my entire user row into Mongo, so I had keys for `:_id` and `:id` in the same document. Once I realized that and played nicely with Mongo's idea of id (same for posts), everything worked out nicely.


Of course, for all that fun with cutting edge technology, there's going to be some sysadmin pain. And there was. I followed the wrong instruction to get Mongo going on this Debian server (followed the Ubuntu instructions. Whoops!) and spent the better part of this morning trying to untangle the mess. Never did, but typed `mongo` and it magically came up somehow. then spent the rest of the day migrating data and trying to figure why the hell ActiveRecord is still looking for a DB even though I ripped out every vestige of AR. Anyway, it's working now, and this will be the first post committed to a non-relational (aka NoSQL) database in my programming career.


Happy times. The amazing thing is that I've kept all of these posts with me through 8 or 9 different databases so far. Yay IBD.


