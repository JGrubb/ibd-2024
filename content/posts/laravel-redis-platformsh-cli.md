---
title: Platform.sh from scratch - Laravel, Redis, and the Platform CLI
date: 2016-10-18
tags: 
  - platform.sh
  - laravel
alias: 299-laravel-redis-platformsh-cli
---

Hello, and welcome back to Platform.sh from scratch. In this post we'll be reconfiguring your Laravel app that we've been working on in the previous posts to use Redis as a cache and session store, rather than the default file store. We'll also install the Platform CLI and use it to SSH into our application container and get a feel for the layout of the filesystem after it's deployed to its environment.

But first, I'd like to have a brief chat about Git...

---

## Using the tools the way they're meant to be used

Ok, we've gotten this far and we're feeling good about life, but we aren't really doing anything that mindblowing yet. We've spent two posts configuring an app to run on a new hosting vendor, whooptidoo. Now, don't get me wrong - we specified our entire project's infrastructure in code. We are free to change around our project's infrastructure however we see fit, without having to file a ticket and wait for support to change it for us. And yet, we're still working on the Git branch that represents the production state of our website, aka "Master". 

Before we continue on, let's put a little insurance in place, courtesy of Git and Platform.sh.

Go to your project admin screen and click the "Branch" button, which is the orange one in the top right. Name this new branch "dev", or really whatever you prefer.

Now say this out loud - 

- I will never push straight to master again
- I will never push straight to master again
- I will never push straight to master again

I'm serious. This is important. You may have worked with Git branches before, and you might be familiar with the stress saving benefits of using them, but you've probably never worked with a hosting vendor who makes it so dead simple to really use them in your day to day development work. In fact, when I first started this job I told people that I worked for a hosting vendor. Now that I understand the power of the tools that we provide I say 

> Platform.sh is a software company that builds tools to make your job as a developer or web application owner easier and less stressful. We also happen to host the sites with which you use our tools.

Now that you've created that branch at Platform.sh you have a byte for byte copy of your master environment, complete with web accessible URL. Any work that you do from now on will land in that `dev` branch before it gets merged into `master`. In this way you'll be able to fully QA and test out new changes before deploying them to production.

This branch only exists at Platform.sh for now, so create and checkout a local `dev` branch to continue on.

`git checkout -b dev`

## A new Redis service!

So far we haven't actually built any logic into this application, nor have we even activated Laravel's built in user authentication feature, so let's go ahead and do that. Following along with the [Laravel docs](https://laravel.com/docs/master/authentication), run this artisan command locally to scaffold out the files that are required.

```
php artisan make:auth
```

You can run the migration locally with `php artisan migrate` but you'll also want to add this to the bottom of your `.platform.app.yaml` file -

```yaml
hooks:
  deploy: |
    php artisan migrate --force
```

Per the previous post, this will run the database migrations for you when you deploy your app on Platform.sh. At this point you can add, commit, and push to Platform.sh and experience the joy of having bona fide user auth in just a few minutes. Thanks Taylor!

Now that you've committed that, let's head back over to the `config` folder and switch from using the default `file` session store in favor of the Redis store. At the top of `config/session.php`, change the `SESSION_DRIVER` setting from `file` to `redis`. As long as we're at it, let's also go to `config/cache.php` and change the default `CACHE_DRIVER` setting to `redis` as well. Now let's set up your app to use Redis.

In your `.platform/services.yaml` file you're going to add a new Redis service - 

```yaml
rediscache:
  type: redis:3.0
```

and in your `.platform.app.yaml` file we're going to add that new service to the `relationships` section - 

```yaml
relationships:
  database: "pgsql:postgresql"
  redis: "rediscache:redis" # this is new!
```

This is all that's required on our end to add a new service to your project, but you'll need to "enhance" your app just a bit to make use of it. In the previous post we added to the top of the `config/database.php` file to enable your app to find the Postgres database that we're using. That file also contains the configuration for Redis, so go there now and change this - 

```php
$config = new Platformsh\ConfigReader\Config();

if ($config->isAvailable()){
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

to this - 

```php
$config = new Platformsh\ConfigReader\Config();

if ($config->isAvailable()){
  $pltrels = $config->relationships;
  $database = $pltrels['database'][0];
  putenv("DB_CONNECTION={$database['scheme']}");
  putenv("DB_HOST={$database['host']}");
  putenv("DB_PORT={$database['port']}");
  putenv("DB_DATABASE={$database['path']}");
  putenv("DB_USERNAME={$database['username']}");
  putenv("DB_PASSWORD={$database['password']}");
  if(isset($pltrels['redis'])) {
    $redis = $pltrels['redis'][0];
    putenv("REDIS_HOST={$redis['host']}");
    putenv("REDIS_PORT={$redis['port']}");
 };
}
```

That is all that's required to enable your app to be able to use the Redis service in your Platform.sh environment. Add, commit, and push!

`git push platform dev`

While that's building, let's install [the Platform CLI](https://docs.platform.sh/overview/cli.html).

---

### The Platform CLI

As the docs say, "the CLI is the official tool to use and manage your Platform.sh projects directly from your terminal. Anything you can do within the Web Interface can be done with the CLI." Fun fact, the project management interface is actually an AngularJS application, and both it and the CLI interact with the same set of APIs on the backend to manage your project. Almost anything that you can do from the UI you can also do from the CLI, and vice versa.

Follow the instructions [in this section](https://docs.platform.sh/overview/cli.html#how-do-i-get-it) to install the CLI, and do make sure you read the rest of that docs page for some more background information.

### Logs!

Let's use the newly installed CLI to check out some logs, since logging is crucial to knowing what's going on inside not just your application but also the environment in which it's running. Running `platform logs` will give you several options for which log you'd like to inspect - 

```
> platform logs
Enter a number to choose a log:
 [0] access
 [1] deploy
 [2] error
 [3] php.access
 [4] php
```

Let's check out the `deploy.log` and see what goes on in there - 

```
[2016-10-03 17:23:01.523855] Launching hook 'php artisan migrate --force'.

Migration table created successfully.
Migrated: 2014_10_12_000000_create_users_table
Migrated: 2014_10_12_100000_create_password_resets_table
```

So the deploy log is actually the stdout output from whatever you have in your `.platform.app.yaml` file in the `hooks.deploy` section. Pretty handy for debugging as you're building up new steps. By default, the `platform logs` command will just `cat` the entire file out to your screen, but you can also pass the name of the log that you want to access as an argument to the command, and pass it a flag `--tail` if you want to tail the log, like so - 

```
> platform logs --help
Command: environment:logs
Aliases: log
Description: Read an environment's logs

Usage:
 platform environment:logs [--lines LINES] [--tail] [-p|--project PROJECT] [--host HOST] [-e|--environment ENVIRONMENT] [-A|--app APP] [--] []

Arguments:
 type The log type, e.g. "access" or "error"

Options:
 --lines=LINES The number of lines to show [default: 100]
 --tail Continuously tail the log
 -p, --project=PROJECT The project ID
 --host=HOST The project's API hostname
 -e, --environment=ENVIRONMENT The environment ID
 -A, --app=APP The remote application name
 -h, --help Display this help message
 -q, --quiet Do not output any message
 -V, --version Display this application version
 -y, --yes Answer "yes" to all prompts; disable interaction
 -n, --no Answer "no" to all prompts
 -v|vv|vvv, --verbose Increase the verbosity of messages

Examples:
 Display a choice of logs that can be read:
 platform environment:logs

 Read the deploy log:
 platform environment:logs deploy

 Read the access log continuously:
 platform environment:logs access --tail

 Read the last 500 lines of the cron log:
 platform environment:logs cron --lines 500
```

### Merging

So your dev brnach should be done building by now. Check out that everything is working the way that you expect, and if it is, let's merge the dev branhc into master. This will constitute a production deployment. You can either click the "Merge" button in your project admin UI, or you can run this from the CLI - `platform merge`. This will give you some interactive output so you can confirm that you're merging into the environment that you want.

One last trick for now - run `platform ssh` from the root of your project. Sure enough, this will SSH you into your application's PHP container, so you can get a feel for what goes on in there. A few tips - 

- The root of the application will be `/app` and the user will be `web`.
- Those very same logs can be found at `/var/log`, just like normal!
- You can check out the generated Nginx config file at the usual location as well - `/etc/nginx/nginx.conf`. This can be \*very\* useful for debugging complex configurations in your `.platform.app.yaml` file.
- You can get a list of running processes the same as normal too - `top`. You'll see that there's not really anything going on in there beyond what your app needs to run, since OS level processes are not running in your LXC container.
- Every app container has a Java executable. There be dragons, but you could theoretically whip up some fairly complex setups with Java dependencies if you ever needed to.

---

That concludes this post, and the series! We'll dive into other features, but with what you've learned in the past 3 posts you should have about 90% of what you need to orient yourself within our product.