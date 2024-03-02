---
title: Speeding up your Platform.sh deploys with Yarn
date: 2016-11-10
tags: 
  - javascript
  - platform.sh
alias: 305-speeding-up-your-platformsh-deploys-with-yarn
---

## Problemspace

You have a decent sized project and your deployments are taking a while. Platform.sh rebuilds your entire application from scratch with each git push so in some cases the process of downloading all those 3rd party packages can take quite a while. We can and do manage local caches of some composer packages due to our PHP heritage, which helps to make `composer install` a pretty snappy affair, but it's simply not possible to effectively do this with Nodejs. 

Compounding this problem for npm is the fact that npm's dependency graph, that is the dependencies of your dependencies, have to be worked out every time you run npm install. This can lead to developers in your org installing different versions of packages which **will** cause you problems. 

Most other package managers overcome this with the use of a "lockfile". A lockfile is a file that's generated when you run `composer install` for the first time, or `bundle install` if you're working Ruby. A lockfile is the result of the dependnecy graph being worked out, and then specifying the **exact** versions of each package. This file is checked into Git, and each dev in the project gets the exact same versions of the packages required for the project.

## Solutionspace

I was listening to the most recent Laravel podcast over the weekend and they got to talking about a new quasi-npm-replacement that had just come out called [Yarn](https://yarnpkg.com/).

Yarn aims to be an almost drop in replacement for npm. There are a number of ways of installing it, but the most simple is just `npm install -g yarn`. My coworkers thought I was trolling them with that, but it makes perfect sense if you think about it.

The only other step is run the `yarn` command locally in order to have Yarn traverse your node_modules directory and build up the Yarn lockfile - `yarn.lock`. Then commit that to git and let's rock and roll on your `.platform.app.yaml`. We're going to require Yarn in the global dependencies section - 

```yaml
dependencies:
  ruby:
    sass: "**" # not required, just assuming
    nodejs:
    gulp-cli: "**" # same here
    yarn: "**"
```

And then replace `npm install` in your `hook.build` with `yarn install` instead, like so - 

```yaml
hooks:
  build: |
    yarn install
    gulp default // for a Laravel project
```

This took my previously 6 minute builds down to about 1 minute. In other words, the time that it took out of my build phase was longer than the time it took to completely move from npm to Yarn in the first place. The reason for this speed boost is that Yarn doesn't have to generate the dependency graph every single time (like npm does) since the lockfile, and Yarn downloads the packages in parallel rather than whatever npm does, which must be one at a time.

If you're using `npm install` as part of your build step on Platform.sh, it's really a no-brainer. Check it out!