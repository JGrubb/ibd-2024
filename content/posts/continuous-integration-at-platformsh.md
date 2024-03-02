---
title: Continuous integration at Platform.sh
date: 2019-09-19
tags: 
  - general development
  - platform.sh
alias: 315-continuous-integration-at-platformsh
---

## WTF is "continuous integration"??

So, back in the old days, building websites was simple. You had plenty of options for how to build one but me personally, I was totally fine with Wordpress and Drupal. They didn't force me to know what was going on under the hood to be productive and they provided a way for me to learn that stuff along the way while feeding my family. I learned CSS on the job while building the first couple things I ever built.

> Cascading Style Sheets (CSS) is a stylesheet language used to describe the presentation of a document written in HTML or XML (including XML dialects such as SVG, MathML or XHTML). CSS describes how elements should be rendered on screen, on paper, in speech, or on other media.
> - https://developer.mozilla.org/en-US/docs/Web/CSS

Being a curious and hungry developer, I soon reached for a tool that I saw discussed called [SASS](https://sass-lang.com/) that was something called a "CSS Preprocessor". Basically it was a computer language that was _like_ CSS, and that _generated_ CSS. It allowed you to reuse bits and pieces of styles in ways that stock CSS did not at the time. 

It was a tool that made your life as a front end developer easier, but there was a catch - now you had to process that SASS into CSS at some point along the way. There was now an extra piece of gear involved in your development -> production lifecycle.

## Another example

Frontend development is insanely complex these days. Plain old Javascript that browsers can digest and execute has exploded like a million suns into various dialects, innumerable frameworks, and more tooling than any sane developer can keep track of. Gone are the days of including jQuery (and my interest in frontend dev along with it).

This is (arguably) great for developers to be more productive and provide high quality experiences to their audience, but there is much, much more to think about in terms of how to _deliver_ those experiences to the end user. Yes, my marketing speak is rather loathesome.

By "deliver" I mean the nuts and bolts - all that Javascript has to go over a network connection from a server in a rack to a user's browser in order to execute and Do Stuff. You don't want your user to have to download 500,000 JS files weighing 40MB just to load your website, so you do a bunch of stuff to optimize that: 

- you smash all those files into 1 file, this cuts down on num_requests because those cost time
- you compress that file into something smaller using gzip or the like, this cuts down on the weight of that code traveling over the wire because that costs time too
- you might have to compile your chosen dialect of JS into "vanilla" JS that the browser can actually execute.

And you're not going to want to do this by hand every time, so you reach for a tool that does it all for you. There is now an extra piece of gear involved in your development -> production lifecycle.

## Just one more

Package managers. They are a wonderful development, and I'm talking about Rubygems, Composer, Pip, and the like. They allow the OSS community to write, publish, and use bits of code (libraries) that other people have written so you don't have to reinvent the same wheel everytime you want to add a login form to your site. Just `gem install devise` and bam, your Rails site has user login forms, password resets, and a ton of other functionality that you didn't have to build yourself.

All of these package managers operate on a similar principle - they have a list of the packages required for a given project and when one of your colleagues adds a new one, it gets added to the list. All you have to do to add it to your local working copy is `composer install` and bam, you have that new library too.

Ideally though, you're not committing that package to your Git repo because that's "3rd party code". It bloats your Git repo which is just a bad smell, but it's also code that you don't own and should never really touch. What _should_ be committed to your Git repo is just _your_ code. At deploy time, just run `composer install` and you're done, but you need to have a Thing that will be able to execute `composer install`.

There is now an extra piece of gear involved in your development -> production lifecycle.

## Continuous Integration

That extra piece of gear is what's commonly referred to as a "build pipeline" or a "continuous integration layer". It's essentially just a process that will execute your instructions for how to tranform your development codebase into your production codebase or _artifact_. 

Plenty of players in the space, starting with a simple Bash script on the user's laptop before pushing to Git. More involved and flexible options include Jenkins, Travis, or CircleCI. Gitlab has a built in CI pipeline, it's part of why they will ultimately win over Github.

Problem with all these tools is that they're _other tools_, and you have to pay for them and maintain them and learn how to use them and deal with API changes etc. Platform.sh has this stuff _all built in_, and we're the only PaaS vendor that I know of that has this level of continuous integration built right in in the form of our [_build_ and _deploy_ hooks](https://docs.platform.sh/configuration/app/build.html).

We have shortcuts for Composer based projects, and full support for adding any number of [build time dependencies](https://docs.platform.sh/configuration/app/build.html#build-dependencies) (probably a whole nother post's worth of material to discuss). Most other PaaS vendors make you buy CircleCI and wire the stuff together in order to achieve the same level of cushy, modern developer experience that we offer, and that's where I've run out of steam on this post. If you got questions, ask em.