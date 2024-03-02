---
title: Reversing strings in PHP
date: 2016-03-28
tags: 
  - php
alias: 277-reversing-strings-php
---

Was asked this question recently, and haven't done any low level string manipulation w PHP in a little while. Couldn't remember the signature of `substr()`, but that wasn't my method anyway. Mine was more like iterating over an index, working from the back forward and concat-ing that on to a new string. Also, this is like 5 minutes worth of code, so cut me some slack.

~~~php

echo "\nString reverse test\n";

function bench_it($func) {
  if (function_exists($func)) {
    $str = file_get_contents(__DIR__ . '/mobydick.txt');
    $results = [];
    $iter = 10;
    for ($i = 0; $i < $iter; $i++) {
      $then = microtime(true);
      $func($str);
      $now = microtime(true);
      $results[] = $now - $then;
    }
    $timeout = array_sum($results) / count($results);

    echo "$func avg: $timeout\n";
  }
}

function substr_test($str) {
  $len = strlen($str);
  $new_str = "";
  while ($len  0) {
    $new_str += substr($str, $len, 1);
    $len--;
  }
}

function str_concat($str) {
  $len = strlen($str);
  $new_str = "";
  while ($len > 0) {
    $new_str += $str[$len - 1];
    $len--;
  }
}

function str_push_and_join($str) {
  $len = strlen($str);
  $arr = [];
  while ($len > 0) {
    $arr[] = $str[$len - 1];
    $len--;
  }
  implode('', $arr);
}

function strrev_test($str) {
  strrev($str);
}
bench_it('substr_test');
bench_it('str_concat');
bench_it('str_push_and_join');
bench_it('strrev_test');
~~~

~~~bash
grubb:php/ $ php test.php [18:18:13]

String reverse test

substr_test avg: 1.2850220918655
str_concat avg: 0.24263381958008
str_push_and_join avg: 0.63150105476379
strrev_test avg: 0.00075311660766602
~~~

So the method that I was working on (#2 str_concat) is the fastest besides the built in `strrev()`, but most interesting is when you run these same tests on PHP 7.0.4 --

~~~bash
grubb:php/ $ brew unlink php56 && brew link php70 [18:18:56]
  Unlinking /usr/local/Cellar/php56/5.6.19... 19 symlinks removed
  Linking /usr/local/Cellar/php70/7.0.4... 17 symlinks created
grubb:php/ $ php test.php [18:19:20]

String reverse test

substr_test avg: 0.12836751937866
str_concat avg: 0.084317827224731
str_push_and_join avg: 0.13263397216797
strrev_test avg: 0.00083370208740234
~~~