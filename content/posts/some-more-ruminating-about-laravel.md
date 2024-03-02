---
title: Some more ruminating about Laravel
date: 2015-05-28
tags: 
  - laravel
  - rails
alias: 261-some-more-ruminating-about-laravel
---

## Expository info (skippable)

So, for nigh 6-7 years now I've been a Rails enthusiast. I bought the PragProg AWDWR book when it was covering Rails 2.x and had the beach and hammock on the cover, and then proceeded to take years to figure out everything the book was actually talking about from the bottom of the stack to the top. I find it very enjoyable to be able to get my ideas out (in code), and Rails is still one of the cushiest frameworks around in terms of ease of use. It's almost as though it were a central tenet of it's philosophy...

For nigh 6 years now, I've been a Drupal professional. Drupal isn't the sexiest platform on the block, but it's been marketed in an absolutely genious manner and has seen incredible traction in sectors that have jobs-o-plenty -- government, educational institutions, publishing, basically anything that needs a robust CMS. I do not, however, find it very enjoyable to work with. The aspects of Drupal that are the most "Drupalistic" - the Form API, render arrays, "configuration as convention" - just feel so out of sync with how the rest of the development world does things. However, see the above point about jobs-o-plenty, and there is still plenty of fun code to be written around the edges of a larger and more mature Drupal installation. 

Drupal 8 is going to come out some day though, and with it will come the invalidation of pretty much everything the wider Drupal community has ever known about writing code for Drupal. It's going to require those of us who enjoy writing code to basically start from scratch with a new framework, only this framework is GIGANTIC and carries with it plenty of interesting architecture opinions and non-opinions alike from it's 12 year history. So I suspect I'm not alone in thinking "if I have to learn an entirely new thing anyway, will learning D8 be the most effective use of my limited time?" 

--

So, given these factors, I've been watching Laravel for a long time, since V3. I haven't really started playing with it until this most recent major release - V5. My initial thoughts were "oh, this is basically PHP on Rails" - I mean this as a complement. But, since I already knew enough about Rails to be effective and have fun, why not spend the time learning Rails better? So that's what I did before the Drupal community at large took up "get off the island" as a mantra.

For the last few months though, I've spent a lot of time building small-ish things with Laravel, and have even had the team begin a Lumen-backed project. I've been going back through the old Laravel podcasts, since I think podcasts are a wonderful way to absorb the philosophy of whichever developer they have on. (Kudos to you Taylor for consistently being on the podcast about your framework. That actually says a lot about your dedication to moving this whole thing forward).

Ok, with that long winded exposition out of the way, a few thoughts...

---

## The aforementioned ruminations


- I like how Laravel is immediately familiar to someone who's worked with Rails down to the API of say, establishing relationships in Eloquent or the `up` and `down` methods on migrations. Almost any server-side framework that has come since Rails in any language has been either an embrace of or a reaction to its widely marketed "opinions". I was listening to an older Laravel podcast where the topic was some haters in the PHP community (supposedly) accusing Laravel of being "too Rails-y" or something. I consider this a plus to the framework (obviously), and I think designing a framework around the opposite paradigm - trying to not make it too Rails-y - immediately builds walls around our various yards in the pan-linguistic developer community. If the rules and the terminology are similar, even if it's in a different programming language, we can all get up to speed with the new thing faster and stop wasting our time learning new concepts that are actually the exact same concepts by a new name.
- I like how Laravel takes Rails' opinionated-ness and actually goes a step further. Rails does not have Auth built into the core. For some reason Rails has Active Job and Turbolinks and Coffeescript, but not Auth. I'd challenge anyone out there to find me a public Rails app without Auth. Building Auth into the framework is an obvious move, and I thank Laravel for doing so. Same goes for billing, or having a Redis cache driver, or having built in support for queued jobs. I was listening to a really old Laravel podcast last night, and Taylor basically described exactly the interface that now exists in L5 - a `Billable` trait that only uses Stripe and that's that. The kind of simplicity that can only come out of being opinionated. The kind of simplicity that Drupal can never have precisely because of it's lack of opinions in the architecture.
- Though it's got its share of fans, I don't get a sense of religion off of Laravel (yet). There's a religion around Drupal, and I think religion is mostly a dangerous thing in that it encourages its followers to follow, but not to ask too many questions of its leaders. The relatively insane pace of development and major version bumps and refactoring of the file structure in these bumps is a double edged sword, but it kinda keeps everyone from getting too set in the "old ways" and offers to me a subconscious clue that Laravel is still casting about for the "righter" way to do things, that nothing is too sacred yet. 
- With that, I like that Laravel is finally offering a LTS release. This will allow me to actually sell it to my boss, and feel better about investing time in really learning the thing. I'm feeling more than a little burned by Angular (talk about learning a mountain of new jargon), but at least the whole Angular 2 flap is a good learning experience for evaluating a new technology. "Be skeptical of shiny new things."
