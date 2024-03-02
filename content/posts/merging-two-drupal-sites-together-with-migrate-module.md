---
title: Merging two Drupal sites together with Migrate Module
date: 2015-01-13
tags: 
  - drupal
alias: 249-merging-two-drupal-sites-together-with-migrate-module
---

_Notes from Mike Ryan, the Migrate guy, at the bottom. Make sure you read them before copying any of this code._

---

The Migrate module has some of the best documentation I've ever seen in DrupalLand, but there are still just a couple of things that I've figured out over the last month that I wish had been clearly explained to me up front. This is an attempt to explain those things to myself.

Clearly, you're going to be writing code to perform this migration. 

- There is a module - [migrate_d2d](https://www.drupal.org/project/migrate_d2d) - that is specifically for stuff like this. It's aware of Drupal content types' basic schema, so it'll save you a LOT of SQL writing to join each nodes' fields on to the base table. 
- You'll write a class for each migration that you need to perform.
- You'll need to write a separate class for each content type that you have.
- You'll need to write classes for roles, users, files, and each taxonomy vocabulary that you have on the site.
- You'll tell Drupal about these migrations by writing an implementation of `hook_cache_clear()` that'll "register" the migrations and make them show up in the GUI and in drush. This looks basically like this -- 

~~~php

function abm_migrate_flush_caches() {

  $common_arguments = array(
    'source_connection' = 'dfi', // the "source" database connection
    // there's a syntax for this that basically mirrors the way that
    // you set up database connections in settings.php
    'source_version' => 7, // a tip to the migrate_d2d module as to which 
    // version of Drupal you're migrating from
  );
 
  $arguments = array_merge($common_arguments, array(
  'machine_name' => 'AbmRole',
  'role_mappings' => array(
  'administrator' => 'administrator',
  'editorial staff' => 'editorial staff',
  'pre-authorized' => 'pre-authorized',
  'directory listee' => 'directory listee',
  'directory listee - preapproval' => 'directory listee - preapproval',
  'directory manager' => 'directory manager',
  'web production' => 'web production'
  ),
));

Migration::registerMigration('AbmRoleMigration', $arguments['machine_name'],
$arguments);

$arguments = array_merge($common_arguments, array(
  'machine_name' => 'AbmUser',
  'role_migration' => 'AbmRole', // forms the relationship between this 
  // user migration and the role migration that already happened.
  // This only works for a few specific, simple cases.
  // Relating nodes with taxonomy items, for example, happens elsewhere.
  // (in the actual migration class...)
));

Migration::registerMigration('AbmUserMigration', $arguments['machine_name'],
$arguments);
 
$arguments = array_merge($common_arguments, array(
  'machine_name' => 'AbmProdCats', // when you run "drush ms", 
  // (migration-status) this is the name that shows up
  'source_vocabulary' => 'product_categories', // yay machine names
  'destination_vocabulary' => 'product_categories'
 ));

Migration::registerMigration('AbmProdCatsMigration', $arguments['machine_name'],
$arguments);
}

~~~

- Registering the migration also creates a set of database tables for each migration, the most interesting of which is the `migrate_map_xxx`, where "xxx" is the `machine_name` of your migration, downcased.

~~~sql

mysql> show tables like 'migrate%';
+------------------------------------+
| Tables_in_for (migrate%) |
+------------------------------------+
| migrate_field_mapping |
| migrate_group |
| migrate_log |
| migrate_map_abmadterms |
| migrate_map_abmappnotes |
| migrate_map_abmarticle |
| migrate_map_abmawardwinners |
| migrate_map_abmblogs |
| migrate_map_abmcompanyprofiles |
| migrate_map_abmdigitaleditions |
| migrate_map_abmevents |
| migrate_map_abmfiles |
| migrate_map_abmnews |
| migrate_map_abmpodcasts |
| migrate_map_abmprodcats |
| migrate_map_abmproductreleases |
| migrate_map_abmproducts |
| migrate_map_abmrole |
| migrate_map_abmtopics |
| migrate_map_abmuser |
| migrate_map_abmvideos |
| migrate_map_abmwebinars |
| migrate_map_abmwhitepapers |
| migrate_message_abmadterms |
| migrate_message_abmappnotes |
| migrate_message_abmarticle |
| migrate_message_abmawardwinners |
| migrate_message_abmblogs |
| migrate_message_abmcompanyprofiles |
| migrate_message_abmdigitaleditions |
| migrate_message_abmevents |
| migrate_message_abmfiles |
| migrate_message_abmnews |
| migrate_message_abmpodcasts |
| migrate_message_abmprodcats |
| migrate_message_abmproductreleases |
| migrate_message_abmproducts |
| migrate_message_abmrole |
| migrate_message_abmtopics |
| migrate_message_abmuser |
| migrate_message_abmvideos |
| migrate_message_abmwebinars |
| migrate_message_abmwhitepapers |
| migrate_status |
+------------------------------------+
44 rows in set (0.00 sec)

mysql> describe migrate_map_abmblogs;
+-----------------+---------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-----------------+---------------------+------+-----+---------+-------+
| sourceid1 | int(10) unsigned | NO | PRI | NULL | |
| destid1 | int(10) unsigned | YES | | NULL | |
| needs_update | tinyint(3) unsigned | NO | | 0 | |
| rollback_action | tinyint(3) unsigned | NO | | 0 | |
| last_imported | int(10) unsigned | NO | | 0 | |
| hash | varchar(32) | YES | | NULL | |
+-----------------+---------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)

mysql> describe migrate_message_abmblogs;
+-----------+------------------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+-----------+------------------+------+-----+---------+----------------+
| msgid | int(10) unsigned | NO | PRI | NULL | auto_increment |
| sourceid1 | int(10) unsigned | NO | MUL | NULL | |
| level | int(10) unsigned | NO | | 1 | |
| message | mediumtext | NO | | NULL | |
+-----------+------------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)

~~~

> [!note]
> Since Migrate is an OOP thing, you can write a parent class for a generic "Node" migration that all of the other specific content types can inherit from. Most of the node migration classes that I wrote look like this, due to most of the fields being set up in the parent class --

~~~php

class AbmBlogsMigration extends AbmNodeMigration {
  public function __construct(array $arguments) {
    parent::__construct($arguments);
    $this-addSimpleMappings(['field_pipes_flag']); // this is the only 
    // blogs specific field that existed on this content type, all of the fields
    // that are common among all content types are mapped in the parent class - 
    // AbmNodeMigration - in exactly the same manner. Except for some that aren't...
  }
}

~~~

> [!info]
> Most fields in a Drupal to Drupal migration will come over easily with `Migration::addSimpleMappings()`, but some require a little more coddling. These are often fields that represent a relationship to another entity - Taxonomy term references, other node references, etc. These will require something like this --

~~~php

php

abstract class AbmNodeMigration extends DrupalNode7Migration {
  public function __construct(array $arguments) {
  parent::__construct($arguments);

  $this-addFieldMapping('field_taxonomy', 'field_taxonomy') // sets up the mapping
  ->sourceMigration('AbmTopics'); // tells you which migration to reference.
  // This makes it look to the migrate_map_xxx table to pull out the NEW 
  // destination primary keys. Without this bit, it'll try to bring over the
  // related entity IDs verbatim, which will either fail because there is no
  // taxonomy term/node/whatever with that ID, or it'll just relate it to the
  // wrong entity. Either way, bad. 

  $this->addFieldMapping('field_taxonomy:source_type') // I wish I could tell you more 
  ->defaultValue('tid'); // about what this part means, but I just don't know yet. 
  // All I know is that is the previous lines are not enough to make it work
 }

~~~


- Speaking of that, prior to finally putting the pieces together about how related entities maintain that relationship, I did lots of clever coding to maintain the relationships between imported entities. It's not that complicated, but I was manually looking into the `migrate_map_xxx` tables to pull destination_ids out. This is obviously wrong abd felt wrong when I was doing it, but it didn't all click until chasing down vague error messages about "field validation errors" in later migrations. It doesn't tell you what fields are in error, it just throws an Exception on these nodes and doesn't save them. I finally ended up dumping `$errors` in `field_attach_validate()` and saw that it was always a related entity field that was erroring. It was easy to figure out after that, but it took me several weeks of getting my head around the rest of it all to be able to get to that very simple point.
- I missed all of that for so long because the user migration has this tidy little line about `'role_migation'` that establishes the relationship, so I thought it would/should be something along those lines. I spent a long time in the module code tracing down default arguments and the like before finally just doing it the hard way. This is wrong.
- Oh, by the way, **USE THE LATEST VERSION OF ALL OF THESE MODULES**. Migrate finally released 2.6, years in the making apparently, a couple of weeks ago, as I was in the middle of all this. I'd been using the previous stable, which is of course missing years of work, and solves almost all problems out of the box.
- Here's another little gem regarding files, and making those relationships tie out --

~~~php

// In AbmNodeMigration::__construct()

 $this-addFieldMapping('field_image', 'field_image') // sets up the mapping
 ->sourceMigration('AbmFiles');
 $this->addFieldMapping('field_image:file_class') // but for some reason it doesn't 
 ->defaultValue('MigrateFileFid'); // work without this part. The answer is
 // here - https://www.drupal.org/node/1540106 - but I haven't had time to 
 // fully absorb that part yet. I glossed over this part of the documentation
 // a dozen times because file_class seems like it'd be unrelated to what you're
 // trying to do - relate nodes and files. file_class sounds like something
 // CSS related. Needless to say, it is not.

~~~

Beer shot - 

![](/uploads/image/image/62/full_2014-12-23_19.52.18.jpg)

---

A review from the guy himself --

>The blog post looks like a good intro to migrate_d2d 2.0, but I'm afraid now it's a bit dated (as you point out towards the bottom).
>
>hook_flush_caches() hasn't been considered a good practice for a while (defining migrations in hook_migrate_api() and using drush migrate-register is preferred - https://www.drupal.org/node/1824884), but I see that migrate_d2d_example still does it - I'll need to update that before the imminent new release so people aren't misled.
>
>Setting the source_type to 'tid' is covered at https://www.drupal.org/node/1224042 - by default the incoming value for a term field is assumed to be the term name, when you're making use of a separate term migration via sourceMigration, the incoming value is a tid and you need to set the source_type so the field handler knows what to expect.
>
>The file_class is similar - normally the value coming in to the file field is assumed to be a URL, but when using a separate file migration and referencing it via sourceMigration it's a fid. The "class" in file_class is a PHP class - the name of any class implementing MigrateFileInterface can be used here.

Thanks Mike!