---
title: Prefect looping - .map() it
date: 2020-11-22
tags: 
  - business
  - databases
alias: 322-prefect-looping-map-it
---

So I'm back in the writing code game after 4 years off. I started a new position about 2 months ago as "director of data engineering" at Platform.sh. It only took ten years longer than originally planned but I have finally unlocked the "some kind of engineering mgmt at an actual tech company" badge.

I'll get into what I'm doing maybe later, but right now I want to leave a breadcrumb for my past self regarding looping over tasks in [Prefect](https://prefect.io).

I'm just kind of tinkering with pulling info from APIs at this point, so the workflow looks basically like this for a couple different vendors:

- Pull a list of accounts
- For each of those accounts, pull a list of services
- For each of those services, pull service metrics for a range of days

This ends up fanning out to potentially thousands of APIs calls ultimately, so I'm doing a lot of `for ... in:` looping on Python. Given that I'm using Prefect though, I'm bumbling up the ladder of how to do these things in a more idiomatic way. Prefect has a native concept of [mapping](https://docs.prefect.io/core/concepts/mapping.html) that reduces the amount of work you need to do in each of these steps as well as makes each iteration of each of these steps more atomic. 

By example - my first, naive approach which was something like this:

- Pull a list of accounts() returns -> list of accounts, then
- Pull a list of services for each of those accounts(list of accounts) loops over and then returns -> list of services
- Pull a list of metrics(list of services) loops over enormous list of services and then returns -> ginormous list of metrics that is the thing we're actually interested in.

Obviously a failure in any one of the loops in the process means the whole thing derails, so I was doing a lot of work trying to handle that. Prefect has a smarter way in their native mapping API. Basically, instead of making the downstream tasks loop over a list of things, you write the downstream task assuming that it will handle 1 of the things in the list. Then instead of calling `do_this_over_and_over_in_a_loop(list)` you call something like `do_one_iteration.map(list)`. Prefect then handles spawning a "child task" for each of the items in the list. This makes is possible to run the loops in parallel, and even to create entire parallel pipelines out of the original "pull the list of accounts" job. It also handles failures in any of the iterations atomically, and doesn't kill the entire loop. 

The whole flow then looks like:

- Pull a list of accounts() returns -> list of accounts
- Pull a list of services for 1 account.map(list of accounts) returns -> list of services for 1 account, then you call map again on the task downstream of _that_
- Pull a list of metrics for 1 service.map(list of services) returns -> the stuff you're really interested in.

the `.map()` bit unrolls each of the lists and creates a child task for each element of the list, then calls the task on that 1 element. 

I'm struggling right now with getting this all running on Platform.sh with [Dask](http://distributed.dask.org/en/latest/), but I'm pretty excited with the potential given my relative rustiness and that I'm working basically alone so far.

More later, <3 love you!! 