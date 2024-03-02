---
title: Platform.sh from scratch - setting up a Laravel app
date: 2016-09-19
tags: 
  - platform.sh
  - laravel
alias: 297-platform-sh-laravel-setup
---

Hi there and welcome back to Platform.sh from scratch. In this post we'll convert a Laravel app for use on Platform and learn a few tricks that will hopefully inform converting **any** app for use on Platform.

---

Step 1 is to get going with a new Laravel app, so follow the instructions on [installing Laravel](https://laravel.com/docs/5.3/installation#installing-laravel) and setting up a new project. Initialize a git repo, add a new platform, and add the Platform.sh git remote to your local repo. All of this is documented in the [previous post](posts/platformsh-scratch-part-1-basic-setup/).

Now, at this point you can try and push code to us but we'll reject it because you don't have any of the Platform config files in place. Let's use [the exact same routes file](https://github.com/JGrubb/platformsh-silex-intro/blob/master/.platform/routes.yaml) as [the previous Silex project](https://github.com/JGrubb/platformsh-silex-intro).

```yaml
# .platform/routes.yaml
"http://www.{default}/":
  type: upstream
  upstream: "app:http"
"http://{default}/":
  type: redirect
  to: "http://www.{default}/"
```

So that's step one. We'll get to the `services.yaml` file in just a minute, but let's go ahead and stub it out - `touch .platform/services.yaml`.

Now let's get to work on the `.platform.app.yaml` file, which will define what your new Laravel app will need to run. One of the key differences between this application and the previous one is that for this one we're actually going to need some writable disk space. Laravel expects a few directories to be present (and to be writable by the web user) in order to write logs and caches and such. We glossed over that bit in the previous post, so I'll now take a moment to talk about Platform.sh's read-only filesystem.

---

> [!info]
> ## Aside - the read-only filesystem
> 
> Platform.sh, like some other cloud PaaS providers, utilizes a read-only filesystem. When your application is deployed, we package up a snapshot of your application code and mount it into it's environment. This means that the days of being able to edit code directly on the server, or of being able to FTP code up to the server are effectively over. 
> 
> All of your app's code must be in Git in order to be deployed, which has quite a few advantages. Not least of them are accountability for who did what and when to your codebase. Of course, you likely need to have some part of your filesystem be writable for logs and file uploads, so we take care of that for you but first let me expand on the benefits of going read-only.
> 
> __Benefit one - consistency__
> 
> [As we've discussed](posts/platform-scratch-part-0), Platform.sh's entire workflow is built around Git. This means that each commit has a SHA hash that identifies each unique commit within your project. If you cut a new Git branch out of another, that new branch will have the same SHA hash as the currently existing branch that you cut it from.
> 
> ~~~
> ~/work/php/magento-platform-sh
> $> git checkout -b test_branch master 93783b2 # <- SHA hash of this commit
> Switched to a new branch 'test_branch'
> 
> ~/work/php/magento-platform-sh
> $> test_branch 93783b2 # <- Same SHA hash
> ~~~
> 
> Platform.sh sees that the two SHA hashes are the same and doesn't bother building your new environment's codebase again since it's already built it. It just uses the already packaged code snapshot from the original branch and creates an environment around it. This saves time, but when it really shines is when it heads in the opposite direction - on merge. When merging a feature branch into a long running develop or master branch, Platform.sh sees that the code snapshot has already been built and deploys that into your master environment. 
> 
> What this means is that you are 100% guaranteed that the code being deployed into your master environment is precisely the same code that you just tested out in your feature branch. Nobody snuck anything new in there via FTP or editing directly, so you can be confident in your deployments. This leads neatly into benefit two...
> 
> __Benefit two - security__
> 
> As you're likely aware there is a large class of exploits, particularly in PHP web apps, that take advantage of that fact that a great many of them allow write access to files that the web server will execute. This means that nefarious users can sometimes find security holes that will allow them to upload executable PHP files to the server and then use those files to gain "elevated privileges", another way of saying "hack your server". With a read-only filesystem, many of those exploits are blocked before they can even happen.
> 
> __Lastly, of course you need to write some files__
> 
> So yes, your web app likely has something in there that needs to be writable. It might be for uploads or it might be for caching or it might be for logs. We got you covered, but you have to specify which directories to make writable in your `.platform.app.yaml` config file. 

So I'm just going to drop the `.platform.app.yaml` in here and explain it bit by bit.

~~~yaml
# the name of this particular app, remember that we allow you
# to create a project out of 1 or more apps, so this gives our
# Laravel app a name...
name: app
type: php:7.0 # SSIA
build:
  # Same as before, this alerts our system to look for a composer.json
  # or composer.lock file and install the dependencies defined therein
  flavor: composer
# basic web configuration for this particular app. Laravel apps have a
# "public" folder that serves as the web docroot.
web:
  locations:
  "/":
    root: "public"
    index:
      - index.php
    allow: true
    passthru: "/index.php"
# How much disk space to allot to this app. 
disk: 2048
# This is where you define your writable file system paths. the keys are the
# paths in your app that need to be writable/uploadable. The values are always
# going to be named "shared:files/$WHATEVER_HERE", where "WHATEVER_HERE" can be
# any arbitrary identifier.
mounts:
  # Laravel uses a directory off the root called "storage" for logs and cache.
  "/storage/app/public": "shared:files/storage/app/public"
  "/storage/framework/views": "shared:files/storage/framework/views"
  "/storage/framework/sessions": "shared:files/storage/framework/sessions"
  "/storage/framework/cache": "shared:files/storage/framework/cache"
  "/storage/logs": "shared:files/storage/logs"
  # And another cache directory here.
  "/bootstrap/cache": "shared:files/bootstrap_cache"
~~~

So this brings us to a decision point. Inside the `storage` directory are nested a few more directories. Laravel sets these directories up for you and then drops a .gitignore inside each of them. This is handy, but presents a small challenge to Platform.sh. Any directories that you declare as writable (or mountable) will be emptied out on the first build and deploy. This means that those nested directories that exist in your git repo will be deleted and you'll be left with an empty `storage` directory. 

This will cause you some headaches when you try to deploy your Laravel app. There are two solutions to this - either add each directory that needs to be writable inside of `storage` to the `mounts` directive or recreate them in a "[deploy hook](https://docs.platform.sh/user_guide/reference/platform-app-yaml.html#hooks)" instead. We'll go with option A but I want to introduce you to hooks, so here's what option B looks like -

~~~yaml
# .platform.app.yaml, after all the rest ...
mounts:
  # Laravel uses a directory off the root called "storage" for logs and cache.
  "/storage": "shared:files/storage"
  # And another cache directory here.
  "/bootstrap/cache": "shared:files/bootstrap_cache"
hooks:
  deploy: |
    mkdir -p storage/app/public
    mkdir -p storage/framework/views
    mkdir -p storage/framework/sessions
    mkdir -p storage/framework/cache
    mkdir -p storage/logs
~~~

This second method here took quite a bit of trial and error to figure out, and either method is valid. It would've taken me a bit if trial and error to go with option A as well, since I'm not very familiar with Laravel.

Now I'll take a moment to explain what hooks are.

## Aside - hooks

Hooks are what they sound like - commands that will execute at certain points in the deployment lifecycle. In this case, we have two - `build` and `deploy`.

Build hooks run while your app is being packaged up, before it's sent to the application's environment. The filesystem is still writable at this point, so if you need to make any modifications to the file structure of your project, this is your chance to do so. Since your project doesn't have an environment yet, it doesn't yet have access to the various services that you've declared in `services.yaml`. So, no database is available at this point.

Deploy hooks run after your project has been mounted into the app environment. The versioned file system is no longer writable, but you do have access to your services at this point. This is typically when you do things like migrate databases or clear caches. 

So you might notice that we're performing those `mkdir` calls in the deploy hook, which seems to contradict what I just said. However, those directories are being created inside of what you've declared as a writable directory in the `mounts` directive, so nothing breaks and indeed you have access to that mounted directory, where you wouldn't have had access in the build hook.

Here's the full `.platform.app.yaml` at this point, for reference.

~~~yaml
name: app
type: php:7.0
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
  # Laravel uses a directory off the root called "storage" for logs and cache.
  "/storage/app/public": "shared:files/storage/app/public"
  "/storage/framework/views": "shared:files/storage/framework/views"
  "/storage/framework/sessions": "shared:files/storage/framework/sessions"
  "/storage/framework/cache": "shared:files/storage/framework/cache"
  "/storage/logs": "shared:files/storage/logs"
  # And another cache directory here.
  "/bootstrap/cache": "shared:files/bootstrap_cache"
~~~

So save that, commit to git and push it to your Platform git remote and you should be on your way. You can find the full repository [here on Github](https://github.com/JGrubb/platformsh-laravel-example). The most important difference between that repo and this project is that we have yet to set this project up with a database connection, which we'll do in the next post.