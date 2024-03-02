---
title: Drupal logstash syslog config
date: 2015-10-04
tags:
  - devops
  - drupal
aliases:
  - 266-drupal-logstash-syslog-config
---

This one really took longer than it needed to.

If you're here, hopefully you've already been through [this lesson on setting up the full ELK stack](https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-4-on-ubuntu-14-04) with Logtash-Forwarder thrown in to boot. For me it pretty much ran as intended from top to bottom, so hopefully you're already getting data into Elasticsearch and are flummoxed by how every single other logstash config out there to parse your syslog data doesn't seem to do the job and is still just treating it like every other syslog message.

#### The rest of the steps to configure Drupal/Logstash

**Drupal's "syslog facility" setting**

This is more or less the key. You have to dig around in Drupal, as well as your webserver and make sure that Drupal is logging to it's own log. By default it'll just go to syslog and then you'll have a hell of a time distinguishing messages from Drupal on the way in. 

If you recall your Logstash-Forwarder config, you tagged the syslog watcher with a `"type": "syslog"` bit. This is really the only info that logstash has at the point that you're setting up your input filters/grok config.

Regardless of Linux flavor, follow this guide to set up the syslog module to point to a logfile of your choosing - [https://www.drupal.org/documentation/modules/syslog](https://www.drupal.org/documentation/modules/syslog). I just copied everything in here, so I now have `/var/log/drupal.log` and it works just fine. The only thing I haven't figured out yet is that now Drupal is logging to both syslog and drupal.log, so somebody tell me how to stop that from happening.

**New Logstash-forwarder config**

You'll just need to a) remove the old syslog watcher from your Logstash-Forwarder (henceforth LF) config and b) tell it to now watch the new drupal.log instead. This took the relevant bits of my LF config from this 

~~~json
 {
 "paths": [
 "/var/log/syslog",
 "/var/log/auth.log"
 ],
 "fields": { "type": "syslog" }
 }
~~~

to this 

~~~json
 {
 "paths": [
 "/var/log/drupal.log"
 ],
 "fields": { "type": "drupal" }
 }
~~~

Don't restart LF just yet, we have to config Logstash to understand what to do with input of "type: drupal" first.

**New Logstash config**

This is where I wasted most of my time over the last few days. I was under the mistaken impression that I could perform some kind of introspection into the fields that were parsed out and then tell Logstash to do this or that with them. As far as I can tell, you'd need to use the "Ruby" Logstash filter to do that, which I didn't feel like this was that complicated a use-case if I could just figure out the "right" way to do it.

Anyway, you've probably already stumbled across this - [https://gist.github.com/Synchro/5917252](https://gist.github.com/Synchro/5917252), and this [https://www.drupal.org/node/1761974](https://www.drupal.org/node/1761974) both of which, annoyingly, show the same useless config (for me, anyway).

My logs look like this -- 

~~~
Oct 4 08:52:34 690elwb07 drupal: http://www.biosciencetechnology.com|1443963154|php|162.220.4.130|http://www.biosciencetechnology.com/news/2011/04/students-prediction-points-way-hot-dense-super-earth||0||Notice: Trying to get property of non-object in abm_metadata_page_alter() (line 41 of /var/www/cascade/prod/brandsites_multi.com/htdocs/docroot/sites/all/modules/custom/abm_metadata/abm_metadata.module).
~~~

The config on that page is presumably looking for a string that begins with "http", which this clearly does not. Here's the config for this particular sequence.

~~~
filter {
 if [type] == "drupal" {
 date {
 match => [ "timestamp", "MMM d HH:mm:ss", "MMM dd HH:mm:ss" ]
 }
 grok {
 match => [ "message", "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: https?://%{HOSTNAME:drupal_vhost}?\|%{NUMBER:drupal_timestamp}\|(?[^\|]\*)\|%{IP:drupal_ip}\|(?[^\|]\*)\|(?[^\|]\*)\|(?[^\|]\*)\|(?[^\|]\*)\|(?.\*)" ]
 }
 }
}
~~~

Now restart Logstash, restart LF, and carry on.