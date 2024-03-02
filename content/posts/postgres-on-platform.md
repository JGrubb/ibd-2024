---
title: Platform.sh from scratch - PostgreSQL
date: 2016-08-23
tags: 
  - platform.sh
  - postgres
  - laravel
alias: 296-postgres-on-platform
---

Hi there and welcome back to Platform from scratch. Today we're going to take a very simple Laravel application that will make use of Postgres on the backend as a database.

The complete example application can be found here - https://github.com/JGrubb/platformsh-laravel-example

The very first step of this will be to add in the appropriate `.platform/services.yaml` file. This file was left intentionally empty in the setup for this Laravel application, as we didn't have a need for a working database and were just getting our app set up and running. Now however, we're going to add a very simple configuration into `services.yaml`.

```yaml
# This is the "name" of this service and can be any arbitrary string.
# You could name this "foo" and that's the name you'd use in your
# .platform.app.yaml file in the next step.
pgsql:
  # This is the actual service you'll be using.
  type: postgresql:9.3
  # How much space you want to give this in megabytes. 
  # 1 gig will get us going
  disk: 1024
```

Over in `.platform.app.yaml` we'll add in the [relationships section](https://docs.platform.sh/user_guide/reference/platform-app-yaml.html#relationships) to the file.

```yaml
relationships:
  # This config takes the form of "relationship_name: service_name:driver"
  #
  # Instead of "database", you could also call this "bar" and that's how it'll
  # show up in the $PLATFORM_RELATIONSHIPS environment variable. I'll show
  # you how that manifests itself in just a moment.
  #
  # "pgsql" in this case is the name you gave it in services.yaml and
  # "postgresql" is the part that you can't just arbitrarily name. That
  # is the hint to our container build system that you need a PG database
  # service.
  database: "pgsql:postgresql"
```

Commit this and push it up to your platform remote. This will trigger a rebuild of your application, and when that's done let's SSH into your environment with the [Platform CLI](https://docs.platform.sh/user_guide/overview/cli/index.html) - `$ platform ssh`.

Once you're in, try this - `echo $PLATFORM_RELATIONSHIPS`. You'll get a big base64 encoded string as a result. This is because you can't set complex objects or even JSON as an environment variable, so let's decode that by piping it to `base64 --decode` - `echo $PLATFORM_RELATIONSHIPS | base64 --decode`

This should give you back something like

`{"database": [{"username": "main", "password": "main", "ip": "250.0.96.171", "host": "database.internal", "query": {"is_master": true}, "path": "main", "scheme": "pgsql", "port": 5432}]}`. 

**sidebar**

Just for the fun of it, let's try this in the `relationships` section -

```yaml
relationships:
  dark_chocolate: "pgsql:postgresql"
```

Sure enough --

```
web@4ikq2xigwlw5s-master--app:~$ echo $PLATFORM_RELATIONSHIPS | base64 --decode
{"dark_chocolate": [{"username": "main", "password": "main", "ip": "250.0.96.171", "host": "big_daddy.internal", "query": {"is_master": true}, "path": "main", "scheme": "pgsql", "port": 5432}]}
```

---

So the basic gist of how you establish a connection to any kind of service that you set up in `services.yaml` should now be clearer than it was before, and we'll now set about adding the code to our Laravel app that will make use of these environment variables.

```php
// at the top of config/database.php. This will decode the base64
// encoded envvar and expand it into the variables that Laravel is
// expecting.
if ($relationships = getenv('PLATFORM_RELATIONSHIPS')){
  $pltrels = json_decode(base64_decode($relationships), TRUE);
  $database = $pltrels['database'][0];
  putenv("DB_CONNECTION={$database['scheme']}");
  putenv("DB_HOST={$database['host']}");
  putenv("DB_PORT={$database['port']}");
  putenv("DB_DATABASE={$database['path']}");
  putenv("DB_USERNAME={$database['username']}");
  putenv("DB_PASSWORD={$database['password']}");
}
```

This particular piece of code is **not** Postgres specific, and in fact will work just fine with MySQL as well. The beauties of abstraction...

The final step in this process is optional, but if you want to have `artisan` migrate the database on deploy rather than logging into the server and running it manually you'd add this to the bottom of your `hooks.deploy` in `.platform.app.yaml` --

```yaml
hooks:
  deploy:
    # other commands
    php artisan migrate --force
```

The `--force` flag will allow migrate to run in a "production" environment.

There is one final step to be aware of, and that's that the `pdo_pgsql` extension is [not enabled by default in the PHP containers](https://docs.platform.sh/user_guide/reference/toolstacks/php/configure-php.html). You'll need to add this somewhere in `.platform.app.yaml` -

```yaml
runtime:
  extensions:
    - pdo_pgsql
```

If you were using MySQL, this step would not be needed as `pdo_mysql` is enabled by default. Indeed, if you're using Postgres, you can disable the MySQL extension if you wish -

```yaml
runtime:
  extensions:
    - pdo_pgsql
  disabled_extensions:
    - pdo_mysql
```

For reference, here's the complete `.platform.app.yaml` --

```yaml
name: app
type: php:5.6
runtime:
  extensions:
    - pdo_pgsql
build:
  flavor: composer
disk: 2048
web:
  locations:
    "/":
      root: "public"
      index:
        - index.php
      allow: true
      passthru: "/index.php"
mounts:
  "/storage": "shared:files/storage"
  "/bootstrap/cache": "shared:files/bootstrap_cache"
relationships:
  database: "pgsql:postgresql"
hooks:
  deploy: |
    mkdir -p storage/app/public
    mkdir -p storage/framework/views
    mkdir -p storage/framework/sessions
    mkdir -p storage/framework/cache
    mkdir -p storage/logs
    php artisan migrate --force
```