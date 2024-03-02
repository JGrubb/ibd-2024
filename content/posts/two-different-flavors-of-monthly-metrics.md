---
title: Two different flavors of monthly metrics
date: 2023-04-26
tags:
  - business
  - analytics
aliases:
  - 345-two-different-flavors-of-monthly-metrics
---

Supposing a hypothetical organization that sold a product whose feature set and COGS closely followed a typical CSP like Amazon Web Services. That organization allows its customers to change products at will but must manually invoice a significant percentage of those sellables, therefore it needs a robust system to track changes to those sellables and ensure that they are properly charged at each turn of the billing cycle.

I'm picturing reporting format that reports on 2 different types of metrics - 

- Accruing (non-temporal)
- Static (temporal)

Accruing metrics are easy, they're things like outgoing bandwidth. These are capped monthly and overages should be trued up, therefore 2 measurements could be helpful on these -

- Month to date sum (this will end up being your billable, since the bounds of the billing cycle are likely set at the calendar month)
- Rolling 30 day average (typical month's usage, helps you notice a customer who is tracking above what you sold them)

Static metrics require a bit more understanding. These are things like CPU cores in a given VM that you're selling. The tin says “8 CPUs” and gives you a monthly rate for those 8 CPUs but you're allowed to upsize that 8 core machine any time you want. Those 8 cores might become 16 for a week, then back to 8. That means you're charging for neither the 8 core machine nor the 16 core machine, but a blend of both.

This is what I mean by “temporal”, you have to generate a time component, divide your 8 or 16 cores into that time component, prorate the usage by that time component, and ultimately arrive at a piece of usage that accrues just like the other.

Given the example of 8 cores to 16 cores and using a 30 day month (720 hours) we get something like this:

You're actually charging for CPU/Hours, firstly. If an 8 core machine is $720/month ($1/hour) and a 16 core machine is $1440 ($2/hour) then your hourly CPU rate is $.0125/hour. This makes it very simple to track (and bill!!) the changes to your sellables that your customers are using.

The metrics you might want to watch on these types of sellables/COGS are almost the opposite of the accruing type:

- Month to date _average_
- Rolling 30 days _average_

The monthly vs. the 30 day average would tell you if they are tracking above or below recent historical averages. It would be trivial to compare the two and throw an alert if the month to date or shorter term rolling average is trending significant above the 30 day average.

_Note: I'm on vacation and just want to remember some stuff for when I get back so don't dock me on this, I'm just spitballing_