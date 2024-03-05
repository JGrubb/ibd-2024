---
title: Platform.sh from scratch, part 0 - explaining Platform as simply as possible.
date: 2016-08-17
tags: 
  - general development
  - devops
  - theory
  - platform.sh
alias: 294-platform-scratch-part-0
---

Hello, and welcome to "Platform.sh from Scratch". In this prologue to the series, I'll go over some of the very highest level concepts of Platform.sh so that you'll have a clearer understanding of what this product is and why it came to be.

Platform.sh is a "Platform as a Service", commonly referred to in this age of acronyms as a "PaaS". The platform that we provide is essentially a suite of development and hosting tools to make developing software applications a smoother end-to-end process. In order to understand what this means though, I'm going to have to go into some detail in this first sidebar. Skip this if you're comfortable with this post so far.

---

> [!abstract] 
> ## PaaS
> 
> Everyone has heard of Salesforce. Salesforce has come to be the poster child for what is now referred to as "SaaS" - Software as a service. Prior to the SaaS era if you wanted a piece of software, be it a video game or Quickbooks or anything else, you had to drive to a store and buy a box with some disks in it. Once the internet reached a level of market penetration into people's homes though, those stores went out of business. This is an obvious evolution in hindsight. SaaS is a high level thing - it's a runnable piece of software that you'll access over the internet via a URL. You might be able to modify/config it a little bit, but will never be your entire business. It's not *your* product. It's someone else's and will likely play some fractional part in your overall business plan.
> 
> *Almost* everyone by this point has heard of Amazon Web Services - AWS. AWS is basically what people are talking about when they say "The Cloud". AWS is a suite of products that emerged from Amazon when they figured out that they needed a huge amount of datacenter capacity to be able to withstand massive retail events like "Black Friday" and "Cyber Monday", and that for most of the rest of the year they had tons of excess capacity sitting around draining money from their wallet. What to do with all that excess capacity? Sell it to someone else. 
> 
> This relatively simple premise has evolved over the last 10-12 years into numerous products from S3 (basically a giant, limitless hard drive in the sky) to EC2 (basically a giant, limitless hosting server in the sky) to Redshift (basically a giant, limitless database that can be used for data warehousing) to SES (a simple service that sends emails) to an ever growing host of other services that always seems to come out just before your start-up figures out that it needs them.
> 
> AWS and "the cloud" in general is often given the acronym "IaaS" - infrastructure as a service. They're selling you the low level hardware abstractions that you can assemble into an infrastructure on which to run *your* software and by extension *your* company. It requires a decent bit of specialized knowledge for how to use the individual pieces as well as how to plumb them together, but for all intents is infinitely flexible. It's this level that has had most of my interest for the past few years.
> 
> In the middle of these two is what's called "platform as a service" - PaaS. This is what Platform.sh is - a suite of software and hosting services that lets you efficiently build and develop *your* software application, and then *deploy* your software application to a hosting environment that doesn't require as much specialized knowledge on how to plumb all the pieces together. Nor does the hosting environment require you - and this is a most important detail - to set up monitoring and alerting for if something goes wrong in the public environment. 
> 
> The PaaS takes elements of both IaaS and SaaS to allow you to build your software product but not have to hire an extra person just to know the low level server business.

So, back to the program. The development tool set of Platform.sh is entirely based around Git. Just in case the reader is not already familiar with Git, I should explain this a little bit.

---

> [!abstract]
> ## Git
> 
> Software projects are typically composed of **lots** of files. If you want to add a new feature, you might be required to make changes in more than one of those files. Of course, before you get started you'll want to make some kind of backup just in case. If it turns out that the change was buggy or unneeded and you want to revert back to a previous state, you'd just restore those few files back to their previous versions. 
> 
> What if, however, you're working with a bunch of different people and more than one person is working on that change (an utterly common scenario)? How do you manage those backups between all those people? Saving copies of files is basically impossible to manage after a very short while, so out of this need SCM (source code management) was born. It's been through several different iterations by this point, and at this point in time the version of SCM that is leading the market is called [Git](https://git-scm.com/).
> 
> Git is pretty cool. It basically takes snapshots of your entire project whenever you tell it to. It then keeps track of all those snapshots and lets you share those snapshots among a team of developers. Any snapshot can be reverted, and you can see the full history of every change to the codebase so you can keep track of "what happened when". But wait! There's more!
> 
> This is not an exclusive feature of Git, but it has a feature called "branching". Branching is intuitively named, and is basically the concept of taking a specific snapshot and making changes based off of that one snapshot while other work continues on down the main code line. This is the recommended way to work if you're going to make any kind of significant change to the software, and this method of working allows you to keep the main code line (almost always referred to as the "master branch") in 100% working order. It can be thought of as having a furniture workshop away from your house where you can work and keep the house clean for company to come over at any moment, as opposed to working in the house and risking having a wreck to present should company decide to drop by.
> 
> In essence branching is making a complete copy of your project at a point in time that you can hack on all you like without disturbing anyone else. If and when the change is ready, you "merge" the code back in to the master branch, test it out to make sure everything is still groovy and then you can release the feature or bug fix to the public.
> 
> ```mermaid
> gitGraph
>    commit
>    commit
>    branch develop
>    checkout develop
>    commit
>    commit
>    checkout main
>    merge develop
>    commit
>    commit
> ```


You can read more about the super basics [here](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics) if you wish. For now, all you really need to know is that Git

- Makes it easier to develop software as a team
- Makes it very cheap and easy to try out new features without breaking anything
- Makes it easier to manage changes to your software and to revert back to a known non-broken state

update: Hey look, [a really great post explaining all this](https://blog.codeminer42.com/git-workflow-basics-d405746f6205#.h6wii2hh1) better than I did.

---

**Platform.sh has taken this branching and merging workflow and extended it out into the entire hardware stack.** When you're building a software project of any size, there are considerations beyond just the code your team is writing.

Most applications of any size [connect to some kind of database in the background](posts/chapter-2-databases), this is where they save "data stuff". User uploaded images are a very common thing in the web app world, so if those images aren't there the app will look busted. 

You can branch your code all you like, but you need these other supporting resources to really do your job. Platform.sh makes branches not just of your code, but the entire infrastructure that your project runs on. This allows you to use the common branching/merging workflow with the complete support of everything else that your application depends on.

This may seem like an obvious feature, since how can you develop a new feature without being able to run it (?), but no other service that I know of actually does this. A branch in Git triggers (for all intents) a complete copy of your production site without requiring you to set up any new servers, copy databases over, copy images and everything else, etc, etc, etc. It's a significant hassle to do all this stuff, trust me, and it slows the team down every time you have to do it. Removing this need removes a major friction point in the workflow for building new features on your software product.

### But wait! There's more!!!

This is where it starts getting really, really good. In case you're not aware, there's a website called GitHub. It's where a *whole* lot of folks have decided to host their "git repos" - repo being short for "repository", which is basically that series of snapshots of the state of your project/codebase back to the beginning of time. This is the repo for this blog - https://github.com/JGrubb/django-blog, and here's some of the code that just ran to generate this page you're reading - https://github.com/JGrubb/django-blog/blob/master/blog/views.py#L26-L33. Pretty cool, right? And if I were working on a project with a buddy, we could both use this same repo and work on the same project, whether I'm in Germany or New Jersey or wherever. I can pull his changes over and he can pull mine and this is basically how open source software gets written these days.

The same workflow applies though - if you want to make a new feature or even if you just want to fix a bug, you'd make a new branch and do your work and then "submit a merge request". This basically pings the person who runs the project and says "hey, I would like to suggest making this change. Here's the code I'm changing, maybe you could look it over and if you agree with this change you can merge it in". By way of an example, [here's a list of "pull (aka merge) requests"](https://github.com/platformsh/platformsh-docs/pulls) for the codebase that comprises [the documentation for Platform](https://docs.platform.sh/user_guide/). 

Again, this is how software gets written and it's pretty mind blowing if you think about it. We software developers are so used to it our minds cease to be amazed, but not because it's not amazing. I mean, currently participating in that list of PRs are folks from France, Chicago, Hong Kong, the UK, and so on. Amazing. It is also, however, a pain in the ass. 

It's a pain in the ass because it's typically impossible to tell if something works or not just by looking at the code, so you have to pull their changes over to your computer and test them out somehow. I bet you can see where this is going! Platform has a GitHub integration (BitBucket too) that will automatically build a working version of any merge request that someone opens against your project. That let's you go visit a working copy of the project and test it out without having to do a thing. Now, I don't care how long you've been doing this, **that** is mind blowing. For example, here's Ori's (currently work in progress) PR for adding the Ruby runtime documentation - https://github.com/platformsh/platformsh-docs/pull/339. If you click the "show all checks" link down toward the bottom, it expands with a little link "details". That link takes you to a complete copy of the documentation with Ori's change added to it, so you can read it like you normally would, rather than [reviewing a "diff"](https://github.com/platformsh/platformsh-docs/pull/339/commits/9cc4f9590f3c368441526b8776aef315a468a2df). It's the future now!

What this means in the wider scope is that your time to set up new things to test out new ideas, only to have to tear it down once the tests pass is time that you don't have to waste anymore. You can test changes out and keep right on moving.

This GitHub integration is only one of the really cool and unique features that Platform provides, but this post has gotten absurdly long already. Fortunately, this is intended to be the prologue to this series, so I'll touch on as many of those features as I can as the series progresses.