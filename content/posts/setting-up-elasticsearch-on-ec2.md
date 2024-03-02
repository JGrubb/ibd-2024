---
title: Setting up Elasticsearch on EC2
date: 2015-10-28
tags: 
  - devops
  - elasticsearch
alias: 267-setting-up-elasticsearch-on-ec2
---

Yeah, the pricing on the new AWS ES service is too high for you too, huh? Well just using their service is a heck of a lot easier and possibly cheaper in dev time than trying to set it up yourself. Consider that. But possibly together we can make it over the hump. 

These are the bits that I was stuck on.

---

**Put all your nodes in the same security group**

I have a group for all my EC2 instances that has the appropriate ports opened up.

