---
title: A few things a Drupal dev should know about Ruby
date: 2013-08-10
tags: 
  - drupal
  - ruby
  - devops
alias: 45-few-things-drupal-dev-should-know-about-ruby
---

Hi there, probably-front-end-dev-who's-met-and-used-Sass-and-likes-what-they-see. This is for you.

## RubyGems

Sass is made out of Ruby - it's a very pleasant, general purpose programming language that's pretty easy to learn and like. Ruby has a package management system whereby libraries of Ruby code are bundled up into what's known as "Gems". Sass is a gem. When you install it, you get a couple of new executables to play with in the terminal, namely `sass` and `sass-convert`. The latter of these will help get you started with Sass by converting your straight CSS to Sass. RubyGems inspired PHP's new-but-already-dominant package manager, Composer.

## rbenv

If you are a Mac user, and you are using the version of Ruby that came with your Mac, you are using [a version of Ruby that's actually beyond End Of Life](http://www.ruby-lang.org/en/news/2013/06/30/we-retire-1-8-7/). If all you're ever interested in is Sass, it'll keep working for a while longer but eventually you'll be left behind. A relic. This is the bad news. The good news is that the Ruby community has been working on this problem for a while.

> [!info]
> Because Ruby 1.9 came out a while back and has a bunch of cool new stuff in the form of performance enhancements, syntactic polish, and overall love via it's contributors, and because 1.8 is in life's endzone, and because using outdated versions of open source software just isn't your preferred thing, you'll want to use 1.9. This is how.

The most commonly blogged about solution to this in the Ruby world is [RVM](http://rvm.io/). We're not going to talk about that. We're going to talk about a solution called [rbenv](https://github.com/sstephenson/rbenv). Rbenv is a more recent and lightweight solution to this multiple Ruby versions problem that doesn't require `sudo` to install and update Gems, and allows you to install almost any version of Ruby you desire (of which there are plenty, but that's more than you need to know right now). 

Rbenv works on any *nix based system and installation is [super simple](https://github.com/sstephenson/rbenv#basic-github-checkout)

`$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv`

This installs rbenv, the version manager. Add rbenv to your $PATH - 

`$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile` 

`$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile`

(Zsh users put those two lines in ~/.zshrc).

You might as well go all the way and install the Ruby version installer, a separate tool - ruby-build.

`git clone https://github.com/sstephenson/ruby-build.git \ ~/.rbenv/plugins/ruby-build`

At this point, you'll reload your shell - `exec $SHELL` - and you're ready to rumble. Ruby 2.0.0 was released earlier this year, so unless you really like [living on the edge](http://drupalcode.org/project/media.git), 1.9.3 is a safe bet.

`rbenv install 1.9.3-p448` - (the most recent release as of this time, refer to [changelog](https://github.com/sstephenson/ruby-build/blob/master/CHANGELOG.md)). 

I almost forgot to mention `rbenv rehash` - probably the rbenv command you'll use the most. "rehash" basically tells rbenv to reload itself after you `gem install` any new gem that comes with an executable (like Sass). If you install a new gem and for some reason your computer acts like it has no idea, it's almost certainly this.

Incidentally, both of these tools were written by the same guy - [Sam Stephenson](https://github.com/sstephenson). He works at [37 Signals](https://37signals.com/), the home of [Basecamp](https://basecamp.com/), the original [Ruby on Rails](http://rubyonrails.org/) app, created by a mystical figure known simply by his initials.

### Matz/DHH

Super quick Ruby history lesson...

----

Ruby recently celebrated it's 20th birthday which, without doing any consultation of Wikipedia, makes it roughly the same age as PHP. Ruby's creator and spiritual leader is a guy named Yukihiro Matsumoto, or Matz for short. PHP obviously grabbed it's share of the market more quickly, and Ruby had scarcely gotten off of the island of Japan for about the first half of that until it was catapulted onto the world stage by one man - DHH.

DHH is a charismatic developer from northern Europe with a fondness for business and hair gel. DHH cast off his PHP chains when he found Ruby, created an honest to god framework out of it, open sourced it, and then ran with it. Much of Rails' rise to prominence coincided with the rise of Github and the two together are probably largely responsible for touching off Git's adoption in the greater marketplace.

Rails has impacted the design of almost every single web framework that has come since in any language, either directly borrowing from it's ideas or reacting to it's opinions. Sass came in it's wake, and here we are.

### The Well Grounded Rubyist

If I can recommend one Ruby book to get, it's [The Well Grounded Rubyist](http://www.manning.com/black2/) by David Black.

Black is one of the few western developers who has been doing Ruby since before Rails came along, and is a preeminent authority on the language. This book was my introduction to Ruby's version of OOP, which is indescribably more elegant, consistent, and to-the-point than PHP's, and reads almost like a great novel in the way that it builds in intensity from beginning to end and rewards repeated readings. No, I'm not shitting you.