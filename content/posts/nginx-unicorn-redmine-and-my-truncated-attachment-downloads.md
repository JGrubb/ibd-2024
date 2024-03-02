---
title: Nginx, Unicorn, Redmine, and my truncated attachment downloads
date: 2013-05-22
tags: 
  - devops
alias: 42-nginx-unicorn-redmine-and-my-truncated-attachment-downloads
---

I'm the honcho in charge of our in-house bug tracker - Redmine. Redmine is a rather large Ruby on Rails project, thus nobody in house when I started here had any knowledge or interest in maintaining the thing, since Ruby servers have a bad rap for being kind of finicky to set up, at least in relation to PHP. So it goes. 

I recently upgraded to the lastest stable release - 2.3.1 - and decided to 86 Passenger as our app server in favor of Unicorn. I've been setting up all my Ruby servers with Unicorn lately and find it to be easier than Passenger, even tho ease of deployment is Passenger's whole selling point. I find the Nginx reverse proxying back to a pool of app servers, ala PHP-FPM that's running this site currently to be an easy mental model to get my head around. I get Passenger's mod_rails/mod_php approach, I just prefer the other. 

Anyway, sorry.

So I upgraded the whole infrastructure last week to Ruby 1.9.3-p392, Redmine 2.3.1, and everything went fairly smoothly. I was alerted to a bug this morning though, where attachments were being mangled. Basically, everything was being truncated to the first 48k in the file, and this applied to images as well as PDFs and Excel spreadsheets. I dove into the files/ directory in Redmine and saw that the files were all there, and that the file sizes were correct, so they were getting **to** the server, just not coming back.

I suspected I'd broken something with the new app server, but it took me a while to track it down. I'd previously been running the Nginx process and the Passenger process as the same user on the server, something I changed in this recent deployment. After trotting through all the Unicorn logs, the Rails logs, and what I thought were the Nginx logs, I found some "other" Nginx logs that happened to be the ones that were actually being used now. They were filled with this --

~~~
2013/05/22 14:13:58 [crit] 17604#0: \*9408 open() "/opt/nginx/proxy_temp/7/06/0000000067" failed (13: Permission denied) while reading upstream, client: 65.126.154.6, server: _, request: "GET /attachments/download/2323/AdvertiserEmailLeads_with_Verbiage_Changes.xls HTTP/1.1", upstream: "http://unix:/tmp/redmine.sock:/attachments/download/2323/AdvertiserEmailLeads_with_Verbiage_Changes.xls", host: "redmine.advantagemedia.com", referrer: "http://redmine.advantagemedia.com/issues/4958"

2013/05/22 14:14:43 [crit] 21936#0: \*8 open() "/opt/nginx/proxy_temp/1/00/0000000001" failed (13: Permission denied) while reading upstream, client: 65.126.154.6, server: _, request: "GET /attachments/download/1454/Balluff_112012html5.zip HTTP/1.1", upstream: "http://unix:/tmp/redmine.sock:/attachments/download/1454/Balluff_112012html5.zip", host: "redmine.advantagemedia.com", referrer: "http://redmine.advantagemedia.com/issues/4229"
~~~

So that's a good thing, because we're getting really warm by this point. Basically, the `/opt/nginx/proxy_temp` directory is full of proxy temp files that were still owned by the old nginx user. Now that the Nginx process was running as user `nobody`, the ownership and permissions were wrong. So a `chown -R nobody` on that proxy_temp directory and everything was right with the world.