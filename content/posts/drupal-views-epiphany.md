---
title: A Drupal Views epiphany
date: 2013-12-27
tags: 
  - drupal
alias: 205-drupal-views-epiphany
---
There's a question currently on r/drupal that asks the question "What's the best way to get your head around Views?". There are many excellent answers -- "study the Views API file", "get to know the UI ", "fuck Views because it writes poor queries" -- but none of them, to me, really answer the question.

Views, for the uninitiated, is the open source, community contributed module that is the reason Drupal is the powerhouse that it is currently. Yes, there are many excellent Drupal features that have contributed to it's adoption across the CMS marketplace, but there is no other contrib module that increases Drupal's capabilities so vastly as Views. Views is "a query builder". That means lots of things, since almost any modern CMS is simply a front end to a database somewhere, and [the very act of clicking any button on almost any website means that a database is being queried somewhere in the distance](/2009/04/chapter2-databases). Thus, a "query builder" is a pretty cool tool to have at your disposal. You can contort almost any conceivable feature out of Views if you really know your way around. \*But how do you learn your way around?\* To me, that is what the author of the question was getting at.

Well, if you came to Drupal the way I did - trial and bumbling error, and not via a CS program somewhere - then you might not be surprised to learn that a "View" is a standard feature in most RDBMSs - [Wikipedia has a great entry](https://en.wikipedia.org/wiki/View_(SQL)). In essence, a "view" in SQL is a predefined query. Views allow a DBA (database administrator) to build up a more complex query that they can then hand off to a "normal" user to use in day to day operations. Maybe this query has a several joins and numerous where clauses that are tough to remember but never change, but the business user needs to supply one varying parameter to get the results they want. Another use case might be limiting access to the DB by granting users access to the views and not to low level querying of the DB (for security reasons). Thus, the seemingly awkwardly named "Views module" actually does exactly what it says it does, if you know the terminology.

Thus, the best way to learn Views is to learn SQL itself. Views' strange terminology (contextual filters, relationships, etc) are just different names for standard query features in MySQL or any other relational database system. Once you start poking around the standard Drupal DB schema, and start stepping through how a simple Drupal View is put together, you can start to understand the deeper mechanics of how the code works under the hood. 

When beginning with a new view, the first question you are asked is `Show _______ of type _____ sorted by _______`. This is the bones of a very simple `select` query. `Show _____` is asking which table in the DB is going to be the base of this query, most often it'll stay on the default "content", which means the `node` table. `Of type _____` says `where type = whatever` and `sort by _____` does just that. So you end up with something like ...

~~~sql
SELECT * FROM node WHERE type = article ORDER BY date DESC
~~~

... and you're off to the races. The rest of the Views wizard allows you to refine this query to (hopefully) pull out what you want to display on the site.

The key to learning Views, therefore, is learning the Drupal database structure in general, and how to query it in straight SQL to get what you want. Once you've wrapped your head around how to join the `users` table to the `role` table via the `users_roles` table in order to pull out every user who is an admin via the mysql command line, it becomes much easier to translate this into a much quicker job in the Views UI. Soon you'll notice that the `blocks`, `users`, and `comments` tables are all plural while `node`, `contact`, and `role` are all singular, and then you'll be well down the path of a deeper understanding of what makes Drupal such an absurdly powerful CMS.