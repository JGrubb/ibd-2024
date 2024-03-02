---
title: Where is the Homebrew Memcached config file?
date: 2014-02-25
tags: 
  - devops
  - general development
alias: 227-where-is-the-homebrew-memcached-config-file
drat: true
---

I've finally been getting around to using Redis for caching on this blog and other Rails projects I've got laying around, and I've finally gotten around to Homebrewing PHP 5.5 to use for my local set up at work. Jose's default PHP 5.5 brew doesn't install the new Opcache, so look around and make sure you install that version because it screams. 

I guess you could fairly say that caching has been on my mind a lot lately as I also finally got around to installing and using Memcached here in development with our company's Drupal sites. That's also a separate Jose homebrew install, so look around for that, too. Anyway, after fiddling around with all the Drupal configs that I had commented out previously that dealt with Memcached, I got it up and running. Make that hauling ass. It's a lot of fun. 

So of course, the next step is to install some outdated script that gives me some visibility into the cache statistics. Here's where I get to the point.

---

Out of the box, the Homebrew installation of Memcached gives you 64M, which is also what you get if you install it from Aptitude on Ubuntu. I've finally done enough Ubuntu to get that most config files are stashed somewhere in /etc (/etc/memcached.conf), but where do these files live on the Mac? 

This is the only downside to Homebrew, that most of the brewers (and God bless y'all, not complaining) pick some arbitrarily weird places to stash stuff, and crazy commands to access the executables. `mysql.server start`? What distro does that come from? It just doesn't make a lot of sense, but I guess that's what keeps your mind sharp.

Anyway, I wanted to feed Memcached some more memory, and I couldn't find the config file for the life of me. Maybe you're here for the same reason. I dug pretty deep into Google before I found the answer. It's in `~/Library/LaunchAgents/homebrew.mxcl.memcached.plist`.

Out of the box it looks like this -- 

~~~xml
xml version="1.0" encoding="UTF-8"?

Label
homebrew.mxcl.memcached
KeepAlive

ProgramArguments

/usr/local/opt/memcached/bin/memcached
-l
localhost

RunAtLoad

WorkingDirectory
/usr/local


~~~

So instead of pointing at a config file, you just pass all the config in the startup command. To bump it up, you need to pass in `-m 128`, or however much you want to feed it, like this.

~~~xml
xml version="1.0" encoding="UTF-8"?



Label
homebrew.mxcl.memcached
KeepAlive

ProgramArguments

/usr/local/opt/memcached/bin/memcached
-l
localhost
-m
256

RunAtLoad

WorkingDirectory
/usr/local


~~~