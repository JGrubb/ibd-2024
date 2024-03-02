---
title: Intermediate Drush Hacking - Drush Aliases
date: 2015-07-16
tags: 
  - drupal
  - devops
alias: 264-intermediate-drush-hacking-drush-aliases
---

There are other articles on this topic around the internet, but for some reason I could never completely make the mental connection on how Drush aliases worked until recently. It's actually really simple to get started, but most other articles tend to throw all the options into their examples so it kind of muddies the waters when you're trying to set yours up. By "you/yours", of course I mean "I/mine".

#### Simple

My work is an Acquia hosting client, and we have a multisite setup. Aliases are a natural fit for multisite configs, so let's show that first. 

~~~php
<?php

// put this in a file at ~/.drush/local.aliases.drushrc.php

$aliases['foo'] = array(
 'root' = '/path/to/docroot',
 'uri' => 'local.foobar.com' // the local dev URL
);
~~~

This is all you need to get off the ground and start using aliases locally. If you then run a `drush cache-clear drush` to reset Drush's internal cache, and then a `drush site-alias` you should be presented with a listing of your aliases.

~~~shell
@none
@local
@local.foo
~~~

The key to this scheme, and something that I feel like was inadequately explained to me even after numerous tutorials, is that the name of the file itself defines the particular group of aliases that this setting will speak to. If you put this into `~/.drush/foo.aliases.drushrc.php` then you list of aliases would look like this ---

~~~shell
@none
@foo
@foo.foo
~~~

If you're running multisite, you'll have a few more in there --

~~~php

<?php

// put this in a file at ~/.drush/local.aliases.drushrc.php

$aliases['foo'] = array(
 'root' = '/path/to/docroot',
 'uri' => 'local.foobar.com' // the local dev URL
);
$aliases['bar'] = array(
 'root' => '/path/to/docroot',
 'uri' => 'local.example.com' // the local dev URL
);
$aliases['ibd'] = array(
 'root' => '/path/to/docroot',
 'uri' => 'local.ignoredbydinosaurs.com' // the local dev URL
);

~~~

~~~shell

$ drush sa

@none
@local
@local.foo
@local.bar
@local.ibd

~~~

Ok, whoop-tee-do, what do you do with that? 

Try clearing the cache on one of those sites from anywhere in your file system with `drush @local.foo cc all`, or clear all the caches on every site in that file with `drush @local cc all`. This is helpful out of the box even without multisite since you don't have to be in the drupal file tree to call drush and not get yelled at for "not having a high enough bootstrap level", but this becomes a major time saver in multisite, since the alternative would be `cd`ing around constantly to effect commands from different directories in `sites/\*`.

Nice and simple. Ready to kick it up a notch?

#### Remote servers

Let's run drush commands on a remote server without having to log in!

~~~php
<?php

// how about we put this code into
// dev.aliases.drushrc.php

$aliases['foo'] = array(
 'root' = '/var/www/path/to/docroot',
 'uri' => 'dev.foobar.com',
 'remote-host' => 'devbox.example.com',
 'remote-user' => 'ssh_username'
);

$aliases['bar'] = array(
 'root' => '/var/www/path/to/docroot',
 'uri' => 'dev.example.com',
 'remote-host' => 'devbox.example.com',
 'remote-user' => 'ssh_username'
);
~~~

This would grow your list of aliases thusly --

~~~shell
$ drush sa

@none
@local
@local.foo
@local.bar
@local.ibd
@dev
@dev.foo
@dev.bar

~~~

...and would let you run any old Drush command you want without having to even be bothered with logging in to that server!

[Lots more examples and info out there](https://www.google.com/search?q=drush%20aliases&rct=j), but this should get you started.