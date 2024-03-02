---
title: Platform.sh from scratch, part 1 -  Basic setup.
date: 2016-08-15
tags: 
  - platform.sh
alias: 293-platformsh-scratch-part-1-basic-setup
---

Hello, and welcome back to "Platform.sh from scratch", see also the [first post in the series](posts/platform-scratch-part-0). The goal here will be to augment the [official documentation](https://docs.platform.sh/) with a short tutorial that shows how to set up a project for proper functioning on [Platform.sh](https://platform.sh/). We'll dive into the "why" as little as possible here. For now let's dive straight into the "how".

We're going to start with a very basic application, the [example Silex app on the front page of the Silex website](http://silex.sensiolabs.org/). This will be a standard Composer based project, so we'll need a `composer.json` file to start with.

The project structure will look like this --

```
jgrubb in ~/play/php/silex-test on master λ tree -I vendor
.
├── app
│   └── index.php
├── composer.json
└── composer.lock
```

The `composer.json` file can be created by running `composer require silex/silex`, or you can just copy this into `composer.json` at the root of your project directory --

```js
{
 "require": {
 "silex/silex": "^2.0"
 }
}

```

Run a quick `composer install` and the rest of the dependencies will be pulled down and placed into the standard `vendor` directory. We're going to be using Git here, and in general you don't want to version 3rd party dependencies like those that Composer downloads. Let's create a `.gitignore` file and add the vendor directory to it.

`echo "vendor" >> .gitignore`

The entirety of the application codebase looks like this --

```php
// in app/index.php
require_once __DIR__.'/../vendor/autoload.php';

$app = new Silex\Application();

$app-get('/hello/{name}', function($name) use($app) {
  return 'Hello '.$app->escape($name);
});

$app->run();
```

Very simply, all this app does add a route that responds to requests along the path of `hello/{whatever}`. As long as you've used the same directory structure, you can `cd` into the `app` directory and run `php -S 127.0.0.1:8080` to fire up the local php webserver and then head to [localhost:8080/hello/user](localhost:8080/hello/user).

If all is working as expected, let's head to Platform and get this thing ready for the development process.

---

Navigate to the [platform website](https://platform.sh/) and register a new account. You have 1 month to (freely) evaluate whether or not Platform fits your needs, so let's get going. I'm assuming that you can find your way through the registration and login workflow and find your self back on the ["your account"](https://accounts.platform.sh/user) page, so let's add your SSH public key into your account and that will be all for configuring your account for now. Under "account settings" -> SSH keys you can add a public key. 

![](https://www.ignoredbydinosaurs.com/uploads/image/screencap1.png)

> [!info]
> **Sidebar - SSH public keys**
> 
> This is required for one main reason - it allows us (Platform) to securely authenticate you when you push code to a project. This is standard for most public code repos (GitHub, Bitbucket), and we use this method as well. A massive side benefit of this workflow is that it allows any Platform account holder to invite any other Platform account holder to their project. This means that agencies can invite developers to projects, users can invite other developers to help with their projects, and the overall friction of matters of authentication and authorization on projects is reduced to virtually zero. You won't likely notice this benefit until a little later on, but when you want to share a project (or even a specific branch of a project) with another user no new account/password/validation workflow is required, and work can begin immediately.

Once that's done, let's go back to the main account page and "create a new platform". 

![](https://www.ignoredbydinosaurs.com/uploads/image/Screen Shot 2016-08-15 at 11.28.35 AM.png)

To me this workflow is pretty self explanatory and the defaults are the correct settings for now, so select which region you'd like your project to be hosted in and get through the checkout workflow. Like I said, nothing will be charged for a month, so have no fear. I have to go through this flow as well, and I work here...

After you get through that flow, you'll be dropped into the "projects" admin of Platform.sh. This is where you'll likely be spending the vast majority of your time.

![](https://www.ignoredbydinosaurs.com/uploads/image/Screen Shot 2016-08-15 at 11.55.15 AM.png)

Name your app (I'm creatively naming mine "Example Project"), and then for this project you'll want to choose to "import your existing code". This option will present you with a URL for a git repo to which you'll be pushing your code. Now is the time to initialize a git repo in your codebase.

`git init`

---

> [!info] 
> **Sidebar - infrastructure as code**
> 
> We'll get into the mechanics later, but this would be a good opportunity to explain the overall ethos of Platform.sh which is that you are going to be specifying your infrastructure - that is the underlying software systems on which your project will be running (MySQL, Redis, etc) - in code. You'll be able to manipulate the infrastructure required to run your project in the same way that you manipulate the behavior of your app through writing code, and you'll push these hardware requirements to us in the form of code.

---

Run `git add .`, which will add all 4 files in your project to git, and then `git commit -m "init commit"` to commit your code. After that you'll want to cruise back over to the Platform project admin screen and copy the lines under "Push an existing repository on the command line" and drop them into your terminal. This will add the Platform git server as a remote for your project, so now you have somewhere to push your code. We're almost there!

But we're not totally there yet, there's one more step. You need to tell Platform what your project needs to run or you won't be able to push your code up to us.

All Platform hosted projects require 3 files - `.platform/routes.yaml`, `.platform/services/yaml` and `.platform.app.yaml`. The [routes file](https://docs.platform.sh/configuration/routes.html) is just that - it's sort of like a [front controller](https://en.wikipedia.org/wiki/Front_controller) for your entire project. What this means in practice is that you can have multiple applications running in your project (under different paths), but for now you really just need to route any request to your little example application.

This is a nice standard starting point for any given PHP project, so place this in `.platform/routes.yaml` --

```yaml
"http://www.{default}/":
 type: upstream
 upstream: "app:http"
"http://{default}/":
 type: redirect
 to: "http://www.{default}/"
```

No, this is not the most beautiful file, but all you really need to know about this is that all URLs that enter this project will either have a base URL of

- `www.whatever.foo` and will be routed to your codebase, or they'll be
- `whatever.foo` and will be redirected to `www`. See step 1.

Another file that you need to have in place is the `.platform.app.yaml` file, which is a file that describes the high level requirements of this application. The most bare bones file is all that we need and it'll look like this --

```yaml
# The name param is linked to the "upstream" parameter in
# routes.yaml. If you called the app "foo", then the
# upstream parameter would look like `upstream: "foo:http"`
name: app
# The "type" parameter takes the form "language:version".
# This could be `python:3.5` for example
type: php:5.6
# Look for a composer.lock (or composer.json) and download
# the listed dependencies
build:
 flavor: composer
# How much disk space will this app need? This is primarily used for
# user uploaded assets, so for this application you don't really need
# anything here, 256 would be fine. You can always grow
# this later, so this is a safe starting point. (in MB)
disk: 2048
# Now that a request has gotten this far, how do you want
# it handled? We'll go into more detail about these params
# in a later post. This section can be thought of as
# somewhat analogous to an Apache or Nginx config file.
web:
 locations:
 "/":
 root: "app"
 passthru: "/index.php"
 index:
 - index.php
 allow: true
```

There is [more information on the documentation website about this file](https://docs.platform.sh/configuration/app-containers.html), and it's all worth your time.

The services file will define what other services (this is where MySQL comes in) your app depends on but since we don't need any yet, this can remain empty. It \*does\* need to be there however, or you won't be allowed to push your project, so for now just create an empty file - `touch .platform/services.yaml`.

Your project's file layout should now look like this (excluding git stuff) --

```
├── .platform
│   └── routes.yaml
│   └── services.yaml
├── .platform.app.yaml
├── app
│   └── index.php
├── composer.json
└── composer.lock
```

So with those three files in place, add them and commit them to your git repo --

`git add . && git commit -m "adding platform config"`

`git push platform master` and you are off! If you are still looking at the dialog screen in the Platform project admin, you can click "continue" now and you will (or will shortly) see a log screen of all the relevant activity for this project - git commits and the project creation before that.

![](https://www.ignoredbydinosaurs.com/uploads/image/Screen Shot 2016-08-15 at 6.08.59 PM.png)

---

After this and every successful git push to Platform those files will be analyzed to make sure that the infrastructure that your project requires and the infrastructure that is available to that project are in line. If something has changed or if this is the first time you've pushed code to this project, the environment will need to be set up with the services that are expected. This takes a moment, and then your code will be mounted into the environment. At this point you'll have a running project that you can visit by going to the project admin dashboard and following the "access site" link near the top of the page.

This concludes this step! It may seem like a lot to get a 5 line PHP project going, but think of what you \*didn't\* just have to do - spin up a server, set up a shell environment that feels home-y on that server, set up a LAMP stack, set up a build process for getting your code onto the server in a defined, runnable state, fuss with DNS or local host entries. We haven't even touched on the aspects of Platform that will completely blow your mind, so stay tuned.