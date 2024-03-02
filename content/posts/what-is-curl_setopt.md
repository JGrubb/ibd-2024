---
title: What is curl_setopt()?
date: 2015-06-01
tags: 
  - php
  - general development
alias: 262-what-is-curl_setopt
---

When I first started getting into this, I read a lot on PHP and remember clearly having my eyes go crossed when I came across code like this --

~~~php
<?php
// Example code: Creating Drupal 7 nodes by POSTing from cURL in PHP:

$site = "127.0.0.1/d7";
$user = "someusername";
$pass = "theusersassword";
$crl = curl_init();
curl_setopt($crl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($crl, CURLOPT_USERAGENT, 'PHP script');
curl_setopt($crl, CURLOPT_COOKIEJAR, "/tmp/cookie.txt");
curl_setopt($crl, CURLOPT_COOKIEFILE, '/tmp/cookie.txt');

/// etc, etc, etc
// taken from http://blog.ampli.fi/creating-drupal-7-nodes-with-php-via-the-restws-api/
~~~

So, in a nutshell, Curl is a very popular Linux/Unix command line program for doing things across the internet - downloading things, uploading things, pinging remote servers with requests, etc. Curl has a wealth of options that you can set with command line flags (`-I` to only get response headers, `-D` to post form data, `-X` to specify a HTTP method, etc).

PHP has a built in function for working with Curl, thereby making it easy to programmatically make HTTP calls to other servers. Of course, to really be able to use Curl in a way that's analogous to it's usage on the command line, you need a way to set those flags. `curl_init()` sets all that up for you, and all you really need to do after that is set whatever flags you need in calls to `curl_setopt()`.

See the docs here - [https://php.net/manual/en/function.curl-init.php](https://php.net/manual/en/function.curl-init.php).