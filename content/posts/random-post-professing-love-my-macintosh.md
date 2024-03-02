---
title: Random post professing love for my Macintosh.
date: 2012-06-18
tags: 
  - random
alias: 204-random-post-professing-love-my-macintosh
---

So long story short - I've been doing this thing a few years, I've learned a few tricks and it seems like every single new developer trick I learn about is already set up in the Mac for me.


I just learned about Apache Benchmark. If you don't know, Google it. It lets you ding your webserver with 100 or 100,000 requests to see how it responds under pressure. It's really cool and really simple.


### Please don't do this (more than once).

```
ab -n 100 -c 5 http://www.johnnygrubb.com/

```

That means, hit my server 100 times, 5 connections at a time on the front page. Running this from the same server that the site is hosted on skips the network, which means you really are just testing the response time of the application.

```
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.johnnygrubb.com (be patient).....done


Server Software:        nginx/1.2.1
Server Hostname:        www.johnnygrubb.com
Server Port:            80

Document Path:          /
Document Length:        15489 bytes

Concurrency Level:      5
Time taken for tests:   0.454 seconds
Complete requests:      100
Failed requests:        0
Write errors:           0
Total transferred:      1623300 bytes
HTML transferred:       1548900 bytes
Requests per second:    220.29 [#/sec] (mean)
Time per request:       22.697 [ms] (mean)
Time per request:       4.539 [ms] (mean, across all concurrent requests)
Transfer rate:          3492.19 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       0
Processing:     8   21  31.5     12     209
Waiting:        8   21  31.5     12     208
Total:          8   21  31.5     12     209

Percentage of the requests served within a certain time (ms)
  50%     12
  66%     14
  75%     16
  80%     17
  90%     41
  95%     54
  98%    190
  99%    209
 100%    209 (longest request)

```

I'll let you decipher what all this means, but that's no caching - DB calls and page builds for every request. Nginx is barely opened up, I have one worker process up. You should see it when I turn page caching on, but I'm leaving it off for now. Running this from here at home gives me 



```
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.johnnygrubb.com (be patient).....done


Server Software:        nginx/1.2.1
Server Hostname:        www.johnnygrubb.com
Server Port:            80

Document Path:          /
Document Length:        15489 bytes

Concurrency Level:      5
Time taken for tests:   70.142 seconds
Complete requests:      100
Failed requests:        0
Write errors:           0
Total transferred:      1629080 bytes
HTML transferred:       1553936 bytes
Requests per second:    1.43 [#/sec] (mean)
Time per request:       3507.124 [ms] (mean)
Time per request:       701.425 [ms] (mean, across all concurrent requests)
Transfer rate:          22.68 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       28 2923 1651.5   2810   12004
Processing:     0  535 658.8    320    2739
Waiting:        0    5  21.9      0     153
Total:        375 3457 1638.2   3309   12005

Percentage of the requests served within a certain time (ms)
  50%   3309
  66%   3836
  75%   3990
  80%   4326
  90%   4884
  95%   6387
  98%   9240
  99%  12005
 100%  12005 (longest request)

```

That's how long my internet connection takes to move 100 connections. Anyway, the point is - I had to install that on my server but only knew about it in the first place because it was already installed on this Mac laptop that I bought in 2008.


I bought this 2GB laptop 4 years ago. It was the last big thing I ever will buy with a credit card. I just put a SSD in it over the weekend, and it's gone from being almost unbearably slow to the fastest damn computer I've ever used in my life. I haven't installed anything except the tools I need to do my job. No iTunes. Not importing my mail. I need Spotify to work, so I installed that. Honestly, unless Apple's new commitment to yearly major OS updates screws me or it just melts from use, I don't see myself buying another laptop for at least another few years.


By the way, I'm sure you could melt my server if you really wanted to. *Please don't.*


