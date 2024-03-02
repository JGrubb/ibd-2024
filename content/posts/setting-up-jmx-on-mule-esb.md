---
title: Setting up JMX on Mule ESB
date: 2015-03-05
tags: 
  - devops
alias: 256-setting-up-jmx-on-mule-esb
draft: true
---

The goal - trying to setup Datadog monitoring of a community Mule server that we have. Using configs from the old server weren't working so I had to learn more about Java infrastructure than I intended. It's simple, but it took me several hours of Googling. Basically you do this -- [http://java.dzone.com/articles/remote-jmx-monitoring-mule](http://java.dzone.com/articles/remote-jmx-monitoring-mule).

But dudeman's code is broken, presumably because dzone autolinks everything in that `xsi:schemaLocation` block. I changed the host to `127.0.0.1` and the port to `9000` in this example, but you can make that whatever you want if you are monitoring remotely.

Just in case that link goes away at some point - 

- Create a directory, call it MuleJMXApp
- Drop the following code in, call it `mule-config.xml`

~~~xml
xml version="1.0" encoding="UTF-8"?
~~~

Deploy this directory to your Mule's apps/ directory just like any other Mule app.