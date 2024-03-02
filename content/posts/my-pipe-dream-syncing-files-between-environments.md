---
title: My pipe dream for syncing files between environments
date: 2016-07-08
tags: 
  - devops
alias: 291-my-pipe-dream-syncing-files-between-environments
---

### Problemspace

We recently migrated from using a local Drupal filesystem (Gluster) to using S3 to house our uploaded site assets. This was relatively simple, and killed at least two birds for us, metaphorically speaking. Some of my findings are chronicled in the previous post linked above.

We are loving that we don't have to worry about syncing files between environments anymore, which means that when we are developing a site locally, the image sources are all pointing to their S3 URLs and so everything Just Works. The only tiny problem is that if anyone needs to upload an image in development it goes up to the same production S3 bucket. Obviously this costs us next to nothing, but it bothers my sense of cleanliness. 

### Solutionspace

S3 has a "lifecycle management" feature that will let you Do Stuff with your bucket assets. Do Stuff is things like delete assets after a certain period, or move them to another "storage class", which is not in the scope of this post...

The limitations of their lifecycle mgmt are a major bummer. They can only be applied to directories within a bucket or to entire buckets themselves. They cannot be applied (simply) to individual objects. If they could, then the fix would be simple - have a hook on file uploads that adds a "delete-after" header to objects that are uploaded from anything but the production environment.

I'm starting a new job in a few weeks, and they use Ceph for managing network files. I haven't even gotten on board with this gig yet, so I don't know what's going on behind the scenes, but Ceph *does* have this individual object expiration feature, at least [according to their bug tracker](http://tracker.ceph.com/issues/4099). I'm wondering if this can be brought to bear on this issue, because once you don't have to move or copy files between environments anymore, going back feels kind of anachronistic.