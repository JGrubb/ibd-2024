---
title: Migrating the Drupal public filesystem to S3
date: 2016-06-15
tags: 
  - drupal
alias: 286-migrating-drupal-filesystem-s3
---

### Problemspace

- You're working with a managed hosting provider and have begun to run out of room on the local/networked filesystem. Vendor wants to upsize your storage for a charge.
- You've got several different environments that you're working with (local|dev|prod|etc) and syncing the filesystem between them is an annoying chore in 2016. The various methods out there of proxying these requests don't really excite you.

### Solutionspace

How about moving the Drupal filesystem up to The Cloud? One of Amazon's earliest products in AWS was the [Simple Storage Service](https://aws.amazon.com/s3/) (S3), and one of it's core usecases is serving public files like images for websites, removing the need for storage of the assets and the (admittedly minimal) compute resources to serve them.

We had both of the issues outlined above and have just completed a migration of all our files up to S3, so I thought I'd write down some discoveries.

---

### s3fs module

[This](https://www.drupal.org/project/s3fs) is a rather unfortunately named module, since there is another open source project out there with the exact same name. I knew of it first and so assumed that this module had something to do with that, but that's *not* the case. Btw, I learned this in [Steven Merrill's excellent session at DrupalCon](https://events.drupal.org/neworleans2016/sessions/loft-your-web-platform-clouds-immutable-infrastructure) in May, check out the video if you're still with me.

In a nutshell, s3fs hijacks the Drupal filesystem. You can put both your public and private filesystems up there, simple to do since S3 has a very rich permissions feature set. Just don't deliberately make your private filesystem "public" and you're set. It then rewrites any URLs that would've been to assets on your local filesystem to point to their new location in S3. 

The [setup is pretty straightforward](http://cgit.drupalcode.org/s3fs/tree/README.txt?h=7.x-2.x), so just a few observations. 

- For multisite, you need to override a few things, namely the default setting for "S3 root folder". For our install we needed to separate each sites assets into site specific folders with the same S3 bucket, so we filled that setting in with a string unique to the site, something like "nameofsite.com".
- There are UI buttons for moving your local files up to S3, but the AWS CLI works \*WAY\* faster. [There are a wealth of well-documented options](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html) to pass, but the gist is this --

~~~bash
aws s3 sync . s3://NAME_OF_BUCKET/nameofsite.com/s3fs-public/ --acl public-read
~~~

- After moving the files up to S3, the module needs to be made aware of what files exist up there, so you'll need to [refresh the file cache](http://cgit.drupalcode.org/s3fs/tree/s3fs.module#n264). If you forget to do this part and flip the switch to have s3fs take over the public filesystem, you'll see bad things.
- With a multisite setup, I found it much easier to flip the switch that says "Do \*not\* rewrite JS/CSS URLs". The downside of this is that I have to make sure that random assets in the Drupal filesystem (ie not within public://) also exist in S3, since so many CSS and JS files refer to assets by root relative paths. This is a hack, but that's life sometimes.

~~~bash
// from Drupal docroot
$ rsync -av --prune-empty-dirs --include='\*/' / 
--include='\*.jpg' --include='\*.png' --include='\*.svg' / 
--include="\*.js" --include="\*.css" --include="\*.gif" /
--include="\*.woff" --include="\*.ttf" --include="\*.map" /
 --exclude='\*' . ~/some/destination/dir
~~~

This says "gimme all those file types in the whole Drupal file tree and move them over to some other dir" that I can then use AWS CLI to sync up to S3. You should take the opportunity before running this to delete all your local public:// files, because they'll get sucked up in this command as well. You won't need them anymore after you do this migration anyway.

~~~bash
// from ~/some/destination/dir
$ aws s3 sync . s3://NAME_OF_BUCKET --acl public-read
~~~

All in all fairly simple, and in theory makes our setup much more portable between environments as well as vendors. Another excellent [writeup of this module can be found here](http://www.symphonythemes.com/drupal-blog/easy-amazon-s3-drupal-s3fs).