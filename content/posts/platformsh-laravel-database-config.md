---
title: Platform.sh from Scratch - adding a database to your Laravel app
date: 2016-10-13
tags: 
  - platform.sh
  - laravel
alias: 298-platformsh-laravel-database-config
---

Hello (!) and welcome back to Platform.sh from scratch. In this post we'll learn about how to set up the Laravel app from the previous post to hook in to various services on Platform, starting with a database connection and moving on to using Redis as a cache and session store. Along the way we'll visit Platform.sh's "environment variables" feature, and we'll set up our first fully functioning deploy hook. 

> Prerequisites - go through the previous post and get that far...

Let's get started!

---

So the first step is to add a database to your `services.yaml` file. Let's choose PostgresQL, which is my personal preference for open source databases these days (mostly due to the fact that it hasn't been bought by Oracle and subsequently forked). Add this to your `services.yaml` file, which should currently be empty.

~~~yaml
# adds a Postgres 9.3 service to your project
# and gives it about a gigabyte of disk space
pgsql:
  type: postgresql:9.3
  disk: 1024
~~~

And in your `.platform.app.yaml` add this anywhere -

~~~yaml
# This is how you define service relationships in your application
# I personally think this should've been named "services" but such is life
relationships:
  database: "pgsql:postgresql"
~~~

As you can see, setting up your project to provision new services is super easy and as the platform matures we'll likely support several versions of any given piece of software. This will someday allow users to easily test out upgrading something like a database to a new major version in another branch without worrying about the usual hassles. 

Now we need to set up our application to use these new services. This is fairly straightforward, but feels a little strange the first time so I'll walk you through the general algorithm that you'll use no matter what the framework or language you're using.

Platform.sh encodes many key pieces of information about your application into OS environment variables. If you SSH into your app container you can `echo $PLATFORM_APPLICATION` and get back a long string that's in essence the base64 encoded version of your `.platform.app.yaml` file. Same with `$PLATFORM_ROUTES`. This is how we store metadata about your application, and you'll make use of these variables as well to establish database connections. The basic algorithm for finding DB connection info is

- read and decode $PLATFORM_RELATIONSHIPS into a json string.
- parse that json string into an object and use the attributes of that object to set the connection info

So with that in mind, let's get your Laravel app wired up. 

---

First let's make use of a nice little composer package that Platform.sh has authored in order to simplify this bit. Run `composer require platformsh/config-reader` and install this package. Next let's head to `config/database.php` and add this little snippet to the top -

```php
php // <- leave that out, it's for syntax highlighting only
$config = new Platformsh\ConfigReader\Config();

if ($config-isAvailable()){
  $pltrels = $config->relationships;
  $database = $pltrels['database'][0];
  putenv("DB_CONNECTION={$database['scheme']}");
  putenv("DB_HOST={$database['host']}");
  putenv("DB_PORT={$database['port']}");
  putenv("DB_DATABASE={$database['path']}");
  putenv("DB_USERNAME={$database['username']}");
  putenv("DB_PASSWORD={$database['password']}");
}

```

You can read the source of the composer package yourself, but essentially the `$config` instance has properties for each of the encoded environment variables that Platform.sh adds to your environments. In the case of the relationships attribute, you might have several databases defined, so it's a simple matter of digging into that property to pull that values out.

In the case of Laravel, it makes use of the `vlucas/phpdotenv` package to read certain settings out of environment variables, so it's really just a matter of translating the nested variables that Platform.sh provides into what Laravel is already expecting.

> I suspect it would be astonishingly easy for someone to come up with a drop in "Laravel Helper" package that would set all this up for automatically, but I haven't gotten that far just yet.

With this much code, your app is now ready to connect to the database in whichever environment you'll be deploying so go ahead and commit this.

`git add . && git commit -m "adding platform db config"`

There's one more blocker for your Laravel app that you'll need to take care of before you're really up and running and that's the need for the `APP_KEY` variable to be defined. Time to leran about Platform.sh's environment variables feature!

---

### Platform.sh Environment Variables

You're likely familiar with the situation where you need to make use of some "privileged" data in your application, and that you don't want to store that data in Git. Or perhaps the use case is that you have different settings for certain things in development than you do in production, perhaps a `DEBUG` flag or something like that. The usual solution for these cases is to use OS environment variables (just like we do). We provide a feature easily setting variables that you can read in your environments, so let's set one up for the Laravel `SECRET_KEY`. 

Head to your project's admin page and click on "configure environment", then "Variables". The simplest thing to do is pull the SECRET_KEY out of your local `.env` file, so click "add variable" and give it a name of "SECRET_KEY" and put the value in the "value" field. This will trigger a redeployment of your application.

Last step is to add some code in to your application to read those variables back out. Add this to the top of your `config/app.php` file -

```php

$config = new \Platformsh\ConfigReader\Config();

if($config-isAvailable()){
  foreach($config->variables as $k => $v) {
    putenv("$k=$v");
  }
}

```

Provided you've already set up that SECRET_KEY variable, it'll be read further down that file and you're good to go, so let's commit and push to platform.

```
git add .
git commit -m "adding platform environment variables config"
git push platform master
```

So this post is already a bit long so I'll just touch on one last point quickly. Now that you have this database all hooked up and ready to go, you're likely going to want to use it! Should you want to automate the process of applying your database migrations, you can do that in a simple deploy hook.

```yaml
hooks:
  deploy: |
    php artisan migrate --forcxe
```

The `--force` flag sounds scary but all it does it disable the command needing feedback which of course, you are unable to provide yourself. The other option is to SSH into the app server after deployment and run the command yourself, which I'll demonstrate in the next post.