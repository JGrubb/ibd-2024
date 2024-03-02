---
title: Homebrew Postgres install issues, Mac OS X server
date: 2013-05-29
tags: 
  - postgres
  - devops
alias: 155-homebrew-postgres-install-issues-mac-os-x-server
---
 
```

The program "postgres" was found by "/usr/local/Cellar/postgresql/9.2.4/bin/initdb"
but was not the same version as initdb.

```

I've been battling this for the last couple of hours, trying to figure out why I can't make Postgres run as easily on my desktop as I did on my laptop. Homebrew took care of it all, just leaving me with the agony of taking off the MySQL training wheels to figure out this new and scary Postgres admin syntax.

So I uninstalled the Homebrew version and went to the EnterpriseDB site and downloaded the official installer for Mac. This didn't yield any results either, and seemed to want you to use the GUI tools to administer it anyway. `which psql` kept giving me `/usr/bin/psql`, which should've been more of a clue, but I'm not that quick. `psql --version` kept giving me `9.0.2`, which also should've been more of a clue, but I just figured I must've installed Postgres a long time ago and gave up and forgot about it.

Then I remembered. Mac OSX server comes with Postgres. That's why it's reporting `/usr/bin` for all it's paths instead of `/usr/local/bin` (the homebrew default), or `/Library/Postgres`, the official installer default.

There was also an unkillable set of `_postgres` processes in `ps` that I couldn't figure out how to kill. So, the flamethrower method is to delete everything in /usr/bin that relates to PG - psql, postgres_real, anything you can find. **Don't forget /usr/bin/initdb** because that's what was throwing the above error. Then you can get on with the homebrew installer.