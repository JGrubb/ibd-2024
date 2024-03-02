---
title: Setting up SSL on your Platform.sh site with Cloudflare.
date: 2016-11-30
tags: 
  - devops
  - platform.sh
alias: 307-setting-up-ssl-on-your-platformsh-site-with-cloudflare
---

## Problemspace

You've got a new site on [Platform.sh](https://platform.sh/) that is basically at the end of its development stage, and you're [preparing to go live](https://docs.platform.sh/development/going-live.html). You've decided on [Cloudflare](https://www.cloudflare.com/) to host your DNS. Cloudflare is a good choice for smaller sites, and I recommend it often. Is has a few things going for it - 

- It has a free tier, which gives you pretty much everything you really need for a personal or small business site.
- it has a very robust and modern global network.

One of the main features that a modern DNS provider needs to have in order to work with Platform.sh is somethat that's colloquially known as "CName Flattening". This solves an age-old problem (in internet years) - being able to point your "root domain" to a domain name rather then an IP address. [This post](https://blog.cloudflare.com/introducing-cname-flattening-rfc-compliant-cnames-at-a-domains-root/) explains it better than I can, and that's not eh point of this post anyway.

Another nice feature of Cloudflare is something they call "Flexible SSL". SSL, HTTPS, TLS, they all mean basically the same thing - that the traffic from your user's browser to your website is being encrypted. This is very important in a practical sense if you're ever on a public wifi network, for example. Setting up SSL on your website can be a bit of a headache though, involving buying a cert, generating some rather arcane crytographic keyfiles, installing them correctly on your server, etc.

Cloudflare offers a bit of a relief from this headache with "flexible SSL". That means that your site can use Cloudflare's SSL cert to encrypt the traffic from user to Cloudflare (remember, cloudflare is sitting in between your users and your website's server). Traffic from Cloudflare to your website then travels unencrypted over plain old HTTP. This is "suboptimal", but it does alleviate some of the attack vectors on your users.

```
Cloudflare's "Flexible SSL" option
 HTTPS HTTP
User ----->----> Cloudflare ----->----> "Origin" server (your website)
```

The other alternatives to this are either running your site unencrypted over HTTP or using "full SSL", in which you have to install a cert at the origin in order to encrypt the traffic between Cloudflare and your website.

```
Cloudflare's "Full SSL" option
 HTTPS HTTPS
User ----->----> Cloudflare ----->----> "Origin" server (your website)
```

## Getting there...

So you've been in development this whole time, you're using HTTPS and redirecting HTTP traffic to HTTPS like a good net citizen and now it's time to go live. You figure you'll just skip setting up a SSL and use Cloudflare's Flexible option. You immediately run into a problem though, of a redirect loop. Here's why..

- User requests website over HTTP, gets redirected to HTTPS by your application
- HTTPS travels from user to Cloudflare where it's decrypted and handed back to your website over HTTP
- Repeat until browser crashes and tells you you have a redirect loop.

You have two options at this point - either allow HTTP traffic as well so that traffic can flow from Cloudflare to your server without being redirected, or go full SSL.

Option A will not work very well in the end. Since your app thinks it's running HTTP, all of the in-site generated links will point to the HTTP version of the pages, which means that as soon as someone clicks a link on your site, they'll be on HTTP. No good. 

## Solutionspace

[Get a cert](https://www.namecheap.com/security/ssl-certificates/comodo/positivessl.aspx), [install it](https://docs.platform.sh/development/going-live.html#4---ssltls), and go Full SSL.

## Soon come

A post about using [letencrypt](https://letsencrypt.org/) to set up certs on your Platform.sh project.