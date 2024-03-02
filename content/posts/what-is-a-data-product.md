---
title: What is a "data product"?
date: 2022-09-21
tags: 
  - analytics
  - business
  - data
alias: 337-what-is-a-data-product
---

"Run your data team like it's a product team" was a common refrain at the DBT conference for the last two years. What does that mean? I am still figuring that out, but I have had an aha in the last few weeks about what a "data product" is, exactly.

Your standard software development process requires two main components to deliver the Things - a software developer and her wits. She takes those wits and expresses them into a text editor, and thereby makes something from truly nothing. 

Data products differ in 1 key way - they require raw materials in the form of data. The process of building a data product therefore requires at least 1 additional step that standard software product development does not - refining that data into something consumable by the system that is delivering the product.

There can potentially be an additional step even before this one, which is to get the data in the first place. My current employer built an Observability suite and stack to be able to deliver metrics to our customers about their projects that they run/host here. This process took multiple quarters because the entire metrics creation and delivery pipeline had to be built from scratch. Once the data existed, it was then a process of refining the materials and building the product.

The good news is that many data products can be consumed in a standard way through some kind of BI or reporting or data visualization tool, we use Metabase. It has taken me a while to understand that the method of delivery of the products is the more standardized part, whereas the gathering and refinement of the raw materials/data is where the action is.