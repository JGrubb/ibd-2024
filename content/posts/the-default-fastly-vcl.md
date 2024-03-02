---
title: The default Fastly VCL
date: 2014-10-08
tags: 
  - varnish
alias: 243-the-default-fastly-vcl
---

I screwed this one up pretty bad when I first got started with Fastly. That have that link that says "VCL", as if you should download that to get started. So I did. But that's the _generated_ VCL, and all their special macros are already blown up, so you don't want to copy that one.

And the one on their website has HTML entities encoded, so I'm putting this one here in case I need it again later.

[The original](http://docs.fastly.com/guides/vcl-faqs/how-do-i-mix-and-match-fastly-vcl-with-custom-vcl)

~~~vcl
sub vcl_recv {
#FASTLY recv

  if (req.request != "HEAD" && req.request != "GET" && req.request != "FASTLYPURGE") {
    return(pass);
  }

  return(lookup);
  }

sub vcl_fetch {
#FASTLY fetch

 if ((beresp.status == 500 || beresp.status == 503) && req.restarts < 1 && (req.request == "GET" || req.request == "HEAD")) {
 restart;
 }

 if(req.restarts > 0 ) {
 set beresp.http.Fastly-Restarts = req.restarts;
 }

 if (beresp.http.Set-Cookie) {
 set req.http.Fastly-Cachetype = "SETCOOKIE";
 return (pass);
 }

 if (beresp.http.Cache-Control ~ "private") {
 set req.http.Fastly-Cachetype = "PRIVATE";
 return (pass);
 }

 if (beresp.status == 500 || beresp.status == 503) {
 set req.http.Fastly-Cachetype = "ERROR";
 set beresp.ttl = 1s;
 set beresp.grace = 5s;
 return (deliver);
 }

 if (beresp.http.Expires || beresp.http.Surrogate-Control ~ "max-age" || beresp.http.Cache-Control ~"(s-maxage|max-age)") {
 # keep the ttl here
 } else {
 # apply the default ttl
 set beresp.ttl = 3600s;
 }

 return(deliver);
}

sub vcl_hit {
#FASTLY hit

 if (!obj.cacheable) {
 return(pass);
 }
 return(deliver);
}

sub vcl_miss {
#FASTLY miss
 return(fetch);
}

sub vcl_deliver {
#FASTLY deliver
 return(deliver);
}

sub vcl_error {
#FASTLY error
}

sub vcl_pass {
#FASTLY pass
}
~~~