---
title: My current understanding of our AWS CUR bill
date: 2021-03-15
tags: 
  - business
  - finops
alias: 324-my-current-understanding-of-our-aws-cur-bill
---

Hello, me. This post is to jot down what I think I know about the AWS CUR bill at this point in time. There are entire companies built around helping other companies understand this bill, so this should be considered a primer.

There are several columns that matter in this giant bill, and many dozens that do not matter, and about a hundred or so that fall somewhere in between. The ones that I know about so far that really matter when you're trying to decode a bill AT SCALE are:

- [line item unblended cost](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-U-UnblendedCost)
- [reservation effective cost](https://docs.aws.amazon.com/cur/latest/userguide/reservation-columns.html#reservation-details-E-EffectiveCost)
- [public on demand cost](https://docs.aws.amazon.com/cur/latest/userguide/pricing-columns.html#pricing-details-P-publicOnDemandCost)
- [savings plan effective cost](https://docs.aws.amazon.com/cur/latest/userguide/savingsplans-columns.html#reservation-details-S-SavingsPlanEffectiveCost)

## Line Item unblended cost

Basically everything that is not an EC2 instance that is covered by some sort of reservation or Saving Plan is going to show up in this column - storage, network costs, etc. 

Confusingly, line items that have a Saving Plan applied to them will also show up in this column, making it necessary to check against the "[Line Item Type](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-L-LineItemType)" column to see if it falls under plain old "Usage", in which case you're paying the on demand price. If the Line Item Type column says anything other than "Usage" then it's not what you're actually paying for that resource. 

For resource usage line items that have a Reservation applied to them this column should be $0, but for resource usage line items that have a Savings Plan applied to them this column will contain the full, undiscounted price and you need to look elsewhere to find out what you're really paying.

## Reservation Effective Cost

Any resource usage line items that have some sort of reservation applied to them will show up in this column. This column and the Unblended Cost column are, afaict, mutually exclusive. A line item can have a cost in one or the other, but not both. From the documentation:

> The sum of both the upfront and hourly rate of your RI, averaged into an effective hourly rate.

These line items have a [Line Item Type](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-L-LineItemType) of "`DiscountedUsage`"

## Public on demand cost

This column is mostly useful just for sanity checking as you do your math against the other columns. If you correctly sum up your Savings Plan Effective Costs, your Reservation Effective Costs, and your on demand Unblended Costs then you should have a number that is _lower_ than the sum of this number. 

Ideally it should be much lower, and I have not yet figured out if it is possible for the sum of all your discounted effective costs to be higher than this number. 

If you incorrectly forecast your future usage and purchase discounts in excess of what you actually use, then you're potentially throwing away _more_ money than what you'd be paying on demand. This is the nightmare scenario of course, and the Finops practitioner makes their money by avoiding this for their liege.

## Savings Plan Effective Cost

> The proportion of the Savings Plan monthly commitment amount (Upfront and recurring) that is allocated to each usage line.

This cost column is mutually exclusive with the Reservation Effective Cost, but _not_ with the Unblended Cost. The Unblended column will contain the full, on-demand price for the resource in question but for those with a [Line Item Type](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-L-LineItemType) of "`SavingsPlanCoveredUsage`" the Saving Plan Effective Cost should be used.

I haven't tried this yet, but off the top of my head it would look something like 

```sql
CASE 
	WHEN line_item_type = 'DiscountedUsage' THEN reservation_effective_cost
	WHEN line_item_type = 'SavingsPlanCoveredUsage' THEN savings_plan_effective_cost
	ELSE unblended_cost
END AS actual_cost,

CASE 
	WHEN line_item_type = 'DiscountedUsage' THEN 'Reservation Effective Cost'
	WHEN line_item_type = 'SavingsPlanCoveredUsage' THEN 'Savings Plan Effective Cost'
	WHEN line_item_type = 'Usage' then 'Unblended Cost'
	ELSE CONCAT('Unblended Cost: ', line_item_type)
END AS actual_cost_source
```

Good luck!