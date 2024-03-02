---
title: Resetting postgres sequences
date: 2014-01-27
tags: 
  - databases
  - postgres
alias: 170-resetting-postgres-sequences
---

I recently rebuilt this site (again, I think this is v9 now) into Rails and Postgres. I was moving out of Drupal, so there was a significant downsizing of the database, and some pretty painless migration of the data. Once again, I used the Sequel gem to write the migration script to pull out the posts and related data that gets scattered hither and yon in Drupal's database. That was the easy part.

I did all of this over a period of about two weeks and it was joyous, painless, and fun. I used the [Textacular gem](https://github.com/textacular/textacular) to power that full text search up there (if you're on a `#{$medium-and-up}` device) and generally got to know a teeny little bit more about Postgres - the grown up's open source database. This whole thing is just a step on the path of the continous quest toward "the idea", and some of Postgres' features that let you denormalize some easy data will definitely come in handy when I understand the fundamentals of the engine a little better. I just met one of them now.

#### Postgres sequences

When you want an auto-incrementing id as a primary key on your database table in MySQL, it's very straightforward. It's essentially a property of the column itself, ie 

~~~sql
+-------+---------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+----------------+
| id | int(11) | NO | PRI | NULL | auto_increment |
+-------+---------+------+-----+---------+----------------+
~~~

In postgres, which is a much more robust database, you have an entire "sequence" object available in the database. You can program these sequences as you would an object, so if for some reason you wanted to skip even numbers, you can do that. The first use case for this that comes to mind is sharding your dataset when scaling out, but it'll likely be never when I find that out in practice. 

Anyway, when I migrated the blog data into the system it brought the numeric primary ids with it, but just now when I went to author my first blog post on the new system it bombed out when I went to save. 

~~~
ERROR -- : PG::UniqueViolation: ERROR: duplicate key value violates unique constraint "posts_pkey"
DETAIL: Key (id)=(1) already exists.
~~~

If I hadn't read about sequences already, this would've confused the shit out of me. So the fix is to holler at your sequence object to start incrementing where you need it to, rather than 1, which is where it's currently set.

~~~sql
ibd_db=# select id from posts order by id desc limit 5;
  id
  -----
  251
  250
  244
  243
  239
(5 rows)

ibd_db=# alter sequence posts_id_seq restart with 252;

// Save post, no problems

ibd_db=# select id from posts order by id desc limit 5;
  id
  -----
  252
  251
  250
  244
  243
(5 rows)
~~~

So `ALTER SEQUENCE sequence_name RESTART WITH next_id_in_line;`.