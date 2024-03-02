---
title: Laravel mutator not working
date: 2015-05-07
tags: 
  - laravel
alias: 259-laravel-mutator-not-working
---

I'm sure this is obvious, but I just spent too long trying to figure this one out. The mutator function wasn't even being called. 

Make sure that the field you want to mutate is included in your model's `$fillable` array, else the ORM doesn't care that you want to do things to it.