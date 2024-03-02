---
title: Programmatically creating taxonomy terms in Drupal
date: 2016-06-15
tags: 
  - drupal
alias: 287-programmatically-creating-taxonomy-terms-drupal
---

Because sometimes you need to roll out a bunch of taxonomy terms across 26 sites, and you just don't feel like clicking those buttons.

~~~php

$terms = [
  'iReport',
  'Infographic',
  'Video',
  'Case Study',
  'Application Note',
  'Data Sheet',
];

$vocab = taxonomy_vocabulary_machine_name_load('vocab_machine_name');

foreach($terms as $term) {
  $t = new stdClass;
  $t-name = $term;
  $t->vid = $vocab->vid;
  taxonomy_term_save($t);
}
~~~

Save this to something like `create_terms.php` and then run it with drush!

~~~bash
$ drush @site scr path/to/create_terms.php
~~~