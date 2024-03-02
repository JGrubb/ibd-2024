---
title: DBT - incremental strategies for late arriving data
date: 2023-07-10
tags:
  - analytics
  - DBT
aliases:
  - 346-dbt-incremental-strategies-for-late-arriving-data
---

Hey there, just wanted to leave a signpost for you. My usecases lately have been something like - 

Computing a BIG table, with a lot of math in it, over a LOT of rows of data, and then joining in _other_ data to enrich the primary set. Specifically, this is container usage data, which I'm attempting to blend with our AWS bill to arrive at something like "cost per container" per time period. 

I don't want to have to rebuild this table every day because most of the data is static once it shows up in the warehouse. An incremental strategy would be perfect BUT, some of this data arrives late, which means that if I do the standard DBT routine of 

`WHERE timestamp > (SELECT MAX(timestamp) from {{this}})` 

then I will have gaps. Indeed, I have gaps. I haven't rolled out any reporting on this table, or made any announcements because I felt a disturbance in the force, confirmed by some light analysis this morning.

I've recently discovered a new DBT hammer in the `incremental_strategy` parameter for incrementally built tables, and specifically the `insert_overwrite` option. [From the DBT docs](https://docs.getdbt.com/reference/resource-configs/bigquery-configs#the-insert_overwrite-strategy):

> The `insert_overwrite` strategy generates a merge statement that replaces entire partitions in the destination table.

In short I can just always recompute yesterday and today, or the last 7 days, or whatever full partitions-worth of data I want. Yes, I'm recomputing more than I strictly need to, but it assures me that there will be no gaps in the results. 

This operation seems pretty foolproof so far, check it out.