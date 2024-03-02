---
title: FinOpsLand - Reservations
date: 2023-02-19
tags:
  - business
  - finops
aliases:
  - 344-finopsland-reservations
---

First day of my job at my current employer (almost 7 years ago now) I crack open the company handbook to start onboarding. I remember it saying something like “Reservations are the lifeblood of this company” and thinking “wow, what does that mean?”

I'd had a job prior to this one that had some Stuff on AWS so I was familiar with the concept - something something pay upfront get reduced prices - but it as far from the lifeblood of the company. It was something the IT head took care of, and he didn't seem that pleased to do it either. So, six years after that I find myself in charge of FinOps here and it has a lot to do with reservations. Indeed, reservations are the main lever that you have to pull on the job. Let's talk bout it…

## What are reservations?

I recently heard reservations described as a “forward contract”, which [Investopedia](https://www.investopedia.com/terms/f/forwardcontract.asp) (one of the most useful resources in this leg of my career) describes thusly

> A forward contract is a customized contract between two parties to buy or sell an asset at a specified price on a future date.

The promise of the cloud is that you, developer, can push a button and spin up a VM or any number of other network-connected computing resources that you didn't have to ask IT for. It's why the default pricing model for these resources is called “On Demand”. AWS invented this idea several years ago that if you committed to running that resource for a year or more, you could receive a reduced price. You could pay for the year either entirely upfront to receive the deepest discount, entirely over time as a monthly payment for a shallower discount, or somewhere in between - the “partial upfront”, which mixes an upfront payment with a monthly payment for those resources.

At this point, you might be starting to learn concepts like “amortization” and “the time cost of money” to distinguish why you would choose one of these, but if not I suggest looking them up.

## Uh, ok

There is a balancing act in place with reservations. Obviously you want to receive those lower rates wherever possible, but your resource usage might be, indeed probably is, rather variable. “Elastic”. Suppose you overpurchase a reservation, all upfront so that the entire year is paid for, but then the resource is turned off after 6 months. So my latest analog for what FinOps is largely about is a double ended inventory job. You have an inventory of resources to cover with reservations and you have an inventory of the reservations themselves. One can be created or turned off in an instant, the other lives for 1 or 3 years. 