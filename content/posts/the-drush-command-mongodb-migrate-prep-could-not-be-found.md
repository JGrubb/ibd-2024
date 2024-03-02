---
title: The drush command 'mongodb-migrate-prep' could not be found.
date: 2014-08-23
tags: 
  - drupal
alias: 237-the-drush-command-mongodb-migrate-prep-could-not-be-found
---

For some reason I got it into my head the other day to tinker around with MongoDB and Drupal, masochism I guess. Anyway, I finally had the opportunity to start tinkering with it last night. 

The set up was fairly easy - if you're on a Mac with Homebrew just hit up the josegonzalez builds of PHP5.5 and then `brew install php55-mongo` or something like that. Just `brew search php55-mongo` before you go blindly copying that command in.

Making the connection between Drupal and Mongo was also pretty straightforward, just follow the instructions on [this post](http://spf13.com/post/getting-started-with-drupal-and-mongodb).

After that I was ready to migrate some content, so I did the old `drush | grep mongo` to see a list that looked like this --

~~~
All commands in mongodb: (mongodb)
  mongodb-cli (mdbc) Open a mongodb command-line interface using Drupal's credentials.
  mongodb-conf Print mongodb connection details using print_r().
  mongodb-connect A string for connecting to the mongodb.
  mongodb-query (mdbq) Execute a query against the site mongodb.
Other commands: (adaptivetheme,archive,browse,cache,coder_review,topic,features_plumber,apachesolr_site,redirect,image,libraries,make,mongodb_migrate,nodequeue_generate,print_pdf,queue,rules_scheduler,runserver,search,shellalias,sitealias,ssh,acquia_search,acquia_spi,strongarm,test,usage,uuid,variable,views_bulk_operations,views_data_export,watchdog,xmlsitemap)
  mongodb-migrate Migrates fields. Run mongodb-field-update first.
  mongodb-migrate-prep Prepare for migrate. Resets existing migration. No data is lost.
~~~

So, cool! Only problem is I repeatedly got this back ---

`The drush command 'mongodb-migrate-prep' could not be found. Run 'drush cache-clear drush' to clear the commandfile cache if you have installed new extensions.`

Over and over, clearing drush cache over and over, until I finally figured to look in the drush files that came with the Mongo module. The trick is that Chx actually meant to say that command is called `drush mongodb-migrate-prepare` instead of just "prep".

It's not that reassuring about the experience that lay ahead of me that an error this simple and fixable is lying there unpatched, apparently after 2 years worth of development, since I first tried the green "official" release before going -dev on it. I suppose I'll be submitting a patch for that.

I have another blog post about the migration tribulations, but I eventually got through it and it's kinda cool. Instead of a giant throbbing schema full of `field_data_this_and_that`, all you have is ---

~~~
> show collections
cache
cache_bootstrap
fields_current.file
fields_current.node
fields_current.taxonomy_term
fields_current.user
fields_revision.node
migrate.file
migrate.node
migrate.taxonomy_term
migrate.user
queue.feeds_importer_expire
queue.feeds_push_unsubscribe
queue.feeds_source_clear
queue.feeds_source_import
queue.file_entity_type_determine
queue.print_mail_send
queue.views_bulk_operations
session
system.indexes
~~~

Each node document in that collection has every field that it needs to have, right on the node! Win! Now to figure out what to do with it!!