---
title: Why doesn't 'authorized_keys' work?
date: 2014-02-14
tags: 
  - ssh
  - devops
alias: 223-why-doesn-t-authorized_keys-work
---

For a while now I've been leaving my work computer on 24/7 and remoting in to do all my work when I'm out in the world. I have the same public key on both machines (perhaps not the safest thing in the world, true), so I was confused by why I needed to enter my password every time I logged in to my work computer. Not so confused that I took the time to figure it out until recently.

I had set permissions on the `authorized_keys` file to 400, but for some reason it needs to be 600.

**doesn't work**

`-r-------- 1 grubb staff 410 Nov 27 10:13 authorized_keys`

**works**

`-rw------- 1 grubb staff 410 Nov 27 10:13 authorized_keys`

