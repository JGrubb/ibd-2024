---
title: First experiences with Drupal and HHVM
date: 2013-12-20
tags: 
  - drupal
  - devops
alias: 53-first-experiences-drupal-and-hhvm
---

After reading [the news about Fastcgi support landing in HHVM](http://www.hhvm.com/blog/1817/fastercgi-with-hhvm), I had to finally give it a try. I'll assume that you're at least familiar with HHVM and it's design goals, but if you aren't [the HHVM wiki](https://github.com/facebook/hhvm/wiki) is a good place to start. In a nutshell, it's a new PHP runtime courtesy of Facebook that, if you can get it working, promises to run circles around any PHP interpreter currently on the market. 

So I came into work on Wednesday fired up to give it a try. I wasn't expecting anything at all, since the sites I work with for my day job are pretty large and have a decent number of contrib modules installed. In case you're wondering, core Drupal is supposedly 100% on the HHVM now, but contrib is a different story. 

The first thing you should know is that it only runs on 64-bit version of Ubuntu, so head over to Digital Ocean and fire one up. I prefer 12.04, so that's what I conducted this experiment on. The first link above gives instructions on how to install HHVM via apt, so that's the route I went. I first tried on the tiny little box that this site runs on, which is a 32-bit version of Ubuntu, and while the apt repo would update itself with the new HHVM repo, it wouldn't install. So, onto plan B, which involved a 1G box running a 64-bit version.

This one installed from the HHVM repo without a hitch -- init script in /etc/init.d/ which pointed to some configs in /etc/hhvm. A quick perusal of that config script, which looked very much like an Nginx config, looked pretty straightforward. Installing Nginx and git and everything else I need to stand a site up was routine. Looking good. So `sudo service hhvm start`, and we're off to the races. `top` showed the hhvm process running as `www-data` so I hit the root URL. `Page not found` was the only feedback I got. `curl -I` gave me an `x-something-something` header that said HPHP, so I was puzzled. HipHop was listening on port 80 and was directly catching the web traffic, as opposed to standing behing Nginx and listening on port 9000.

It took me about 30 minutes of fiddling around, but the real clue was in the instructions for how to start HHVM on a system that doesn't have a repo installer. 

~~~
cd /path/to/your/www/root
hhvm --mode server -vServer.Type=fastcgi -vServer.Port=9000
~~~

So, I took another look at the `/etc/hhvm/server.hdf` config file that the init script was pointing to and noticed that it was set to listen on port 80, not port 9000, and it was set inside of a `Server {}` block. That `Server {}` block that looked like the perfect place to put `Type = fastcgi`, so I did, and changed the port to 9000. The docs indicated that the process needs to be started from the root of your PHP app, but that might only apply to non-fastcgi HHVM. I started it from there anyway, and I finally had the thing working.

Standard "standing an existing Drupal site up on a new box" fiddling around with connections and file system settings and I actually got the thing to stand up after about 90 minutes of playing with it.

#### Thoughts

I'm a pretty good sysadmin for a front end developer, but I'm still just a front end developer. It was, however, very easy to find my way around the configs and start to get a sense of what HHVM does. I ended up upsizing the box to a 4G instance for a little bit after it started giving me some memory-related errors that I didn't have the skills to diagnose. I have no idea if that much RAM is needed, but it cost about $.20 to bump it up for a couple hours and find out.

The beautiful thing about it was that once I finally figured out how to stand it up correctly, my existing Nginx/FPM config (a derivative of Perusio's) worked out of the box with absolutely no other intervention. When I got stuck once by a cache clear that I suspect was the real cause of my memory issue, I shut HHMV down, brought FPM up and got unstuck. After I was stabilized again, I shut FPM back down and brought HHVM back up. It was seamless. 

I finally gave up after a little bit because although it was working, the HHVM logs were full of notices, and I repeatedly hit a wall with some function, first in the gd lib and then in apachesolr, that didn't like the input it was being fed. I had to get back to "real work", but I will dive back in very soon and hopefully be able to contribute some feedback. The maintainers are extremely friendly and active on IRC.

I'm very, very excited for the future of this project and would highly recommend giving it a try. I was amazed that I was able to get anything to load on it at all, and am salivating at the thought of actually getting the VM warmed up with a bunch of repeated requests. When it *is* ready for prime time across a wide variety of PHP apps, it's going to change the way people think about interpreted languages in general, and is going to single handedly contribute to a faster web. For this, we thank you Facebook.