---
title: SQL is easy, Data is hard
date: 2023-02-18
tags: 
  - analytics
  - business
  - databases
alias: 342-sql-is-easy-data-is-hard
---

I've been kicking around this thought for a year or so now - to the outsider a career in data looks like a technical path. The data practitioner learns SQL, how to query data stored in a database somewhere using SQL, and if you know _enough SQL_ you can answer any question whose artifacts are stored in that database somewhere.

The reality is that SQL is the very last mile. SQL is code, and so it looks to the non-practitioner like the act of creation, like code written in any imperative language _creates_ motion and process and a webapp or piece of automation that didn't exist before. SQL does not create. SQL _encapsulates_ that which already exists as a business process. 

SQL is a contract. SQL puts business conditions and processes into code. If the business processes are ill-defined, then the SQL that has to be written to handle all the various cases will sprawl. (Most business processes are ill-defined as it turns out, made up in a time of need by a human, and probably one who doesn't spend their day thinking about data modeling.) If the business process is well-defined, but the SQL author's understanding of it is wrong or incomplete, then you'll end up with a poorly written contract that spits out wrong or incomplete answers.

That's what makes Data the hard part, because to write that contract down always requires the author to have spent time reverse-engineering the business process. I view this as an inherent good for the business as a whole - it forces the business to reckon with itself and to better define how it operates. The road to get there is tough though and in my experience it's often the data analyst who is actually pulling the cart.