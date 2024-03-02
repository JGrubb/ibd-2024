---
title: Database migrations with CodeIgniter - 101
date: 2012-05-14
tags: 
  - php
alias: 200-database-migrations-codeigniter-101
---

So I've decided to start playing around with CodeIgniter. It's a supremely simple PHP framework that has a lot of good documentation, a pretty big user/developer base, and has a lot in common stylistically with Rails. I've just really started playing around with it, and wanted to use the migrations feature for building up my database. I won't explain what migrations are or how they work or why to use them because [that's been covered](posts/chapter-6b-laymans-introduction-database-migrations-ruby-rails). 

Our project will be something like a database of shows. So we need a "shows" table in our database. CI needs you to edit application/config/migrations.php to change `$config['migration_enabled'] = FALSE;` to `$config['migration_enabled'] = TRUE;`. This will enable migrations in the app. The next meaningful line down the page - `$config['migration_version'] = X;` will tell your app which migration it's supposed to be on. If you're working with someone else and they update their codebase from a repo, then their app will check their local development database, see that X number of migrations have not been applied (by checking the version number in the migrations table in the DB) and bring the schema up to date. It's neat.

Step 2 will be to write our first migration. Under application/ you'll need a /migrations directory (`application/migrations`). This is where you'll write out the migration file. About here is where you start realizing some of the amazing things that Rails does for you, such as building all of this with a line on the terminal instead of making you trot all over you app to set this up. I digress.

Our first migration will look like this - 

~~~php
// application/migrations/001_add_shows.php
 
php defined("BASEPATH") or exit("No direct script access allowed");

 class Migration_Add_shows extends CI_Migration {
 
 public function up() {
 $this-dbforge->add_field('id');
 $this->dbforge->add_field(array(
 'date' => array(
 'type' => 'DATE',
 'null' => FALSE,
 ),
 'location' => array(
 'type' => 'VARCHAR',
 'constraint' => '255',
 'null' => FALSE,
 ),
 'description' => array(
 'type' => 'TEXT',
 'null' => TRUE,
 ),
 ));
 $this->dbforge->create_table('shows');
 }

 public function down() {
 $this->dbforge->drop_table('shows');
 }

 }
?>
~~~

So here's what this says - 

We're creating a migration called "Add shows". This will create our "shows" table in the DB. It will have an `'id'` column. By using `$this->dbforge->add_field('id');`, CI knows to make this your primary key, to make it an auto-incrementing integer, and for it not to be NULL ([http://codeigniter.com/user_guide/database/forge.html#add_field](http://codeigniter.com/user_guide/database/forge.html#add_field "Database Forge Class : CodeIgniter User Guide")). We're also adding a date field, a location field, and a text description.

The name of this file is important. If you call the class `Migration_Add_shows` and name the file 001_add_show.php (singular), CI won't be able to find it. This is it for step 2.

Note: the down() method is for reversing this change. I haven't figured out how that works just yet from the standpoint of running the down() migration, but you'll want to write the reverse of any up() method for every migration. This is the "undo" button on this process. Ignore it at your peril.

Step 3 is to create a migrations controller, since you'll need this migration to be called from somewhere. I will learn how to run migrations from the command line next, since the CLI is obviously where it's at, but for now we'll write a controller and do it the hard way.

~~~php
// application/controllers/migrate.php
 
php defined("BASEPATH") or exit("No direct script access allowed");

 class Migrate extends CI_Controller {
 public function index() {
 if (ENVIRONMENT == 'development') {
 $this-load->library('migration');
 if ( ! $this->migration->current()) {
 show_error($this->migration->error_string());
 } else {
 echo "success";
 }
 } else {
 echo "go away";
 }
 }
 }
?>
~~~

Now, if you visit yourapp.com/index.php/migrate, you'll have run the migration. It took me a few minutes to get this wired up correctly, and CI gives you some useful error messages, so hopefully you'll be on your way. Basically this says -

This can only be run while in development mode, else "go away" and stop monkeying with my app. Load the migration library, which runs the migration up to the version specified in `config/migrations.php`. When you add a new migration, update the version number in that file, and it'll be run next time you visit this url.

Did you get all that?