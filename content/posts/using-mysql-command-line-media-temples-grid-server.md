---
title: Using the MySQL command line with Media Temple's grid server
date: 2013-01-18
tags: 
  - databases
  - devops
  - mysql
alias: 34-using-mysql-command-line-media-temples-grid-server
---

I've been a Media Temple customer for a long while now, almost 4 years. The file system on my grid server is a timeline of my learning programming and systems administration. I mostly work on big-boy systems over the last 2 years or so, so my comfort level with working through SSH is pretty high now (much higher than working through GUI tools that want to hold your hand, actually). That's one thing that I always loved about MT - the fact that they give you a pretty decent command line experience for administering your server.

One thing that's kind of always gotten on my nerves, however, is the DB admin experience. Lately since it seems like their PHPMyAdmin installation has completely gone to shit. Seriously, it's unusable. I couldn't even restore/import a < 1 MB brand-new-site database the other day because it kept timing out. Of course, this would be a 3 second maneuver on the command line `mysql -u user -p pass target_database < dump.sql`, but they have the command line locked down on the grid server.

I've recently moved a rather involved client project back onto their production server which is hosted on said grid server. I'm still banging on the thing, so I'd like to delete some rows straight out of the DB without having to spend 30 minutes trying to get PHPMyAdmin to do what I need it to. So the other day I went poking around the hosting admin panel.

I'd seen this years ago, and fortunately it's still there, probably just for this use case. In the "global settings" under the "databases" section of your admin panel at the very bottom of the screen is an option to allow external IP addresses access to the DB server. It even has a handy option to "use current IP" to prefill the field for you. Do this.

Next, invoke `mysql` locally, but pass it the `--host` option, like this --

`mysql -u user -p pass --host=external-db.s123456.gridserver.com`. 

You're welcome. 

PS - I tried importing the aforementioned <1 MB db in this method to no avail. Don't know if their db servers were on the blink at the time, but it definitely wasn't bandwidth.

PPS - it has occurred to me since this morning that mysql doesn't run on localhost, that's why you can't login as you normally would. Thus, if you were logged into your webserver, you could pass the same `--host` argument and specify the `internal-db` host to carry on about your business. 

Never said I was that good at this stuff.