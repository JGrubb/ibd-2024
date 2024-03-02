---
title: Easy Markdown with Syntax Highlighting, PHP Edition
date: 2016-11-08
tags: 
  - laravel
  - php
alias: 300-easy-markdown-with-syntax-highlighting-php-edition
---

### Easy Markdown with Syntax Highlighting, PHP Edition

Hi there, and welcome back to this 14th installment of "I rewrote my blog in another framework that I'm interested in learning, this time in Laravel". The trick that we'll be exploring today is that, in contrast to Python (the last version was in Django), PHP's lib story is a bit more sparse for this exact use case. However, I'm completely pleased with the outcome, so let's get busy!

## PHP Markdown

Google that term and you'll find this - [https://michelf.ca/projects/php-markdown/](https://michelf.ca/projects/php-markdown/). This appears to be basically the most robust and well maintained Mardown parser for PHP, so that's where I started. It's quite simple to add to a Laravel project - `composer require michelf/php-markdown` and then (for the purposes of syntax highlighting) you'll want to use the `MarkdownExtra` class. Here's the Laravel code for rendering this article body that you're reading right now - 

```php
public function rendered_body() {
	$parser = new MarkdownExtra();
	$parser->code_class_prefix = "language-";
	return $parser->transform($this->body);
}
```

Pretty darn simple. The only option `code_class_prefix` will be explained shortly.

## Syntax Highlighting

In contrast to [my previous post on the matter](posts/easy-markdown-and-syntax-highlighting-django), the PHP landscape does \*not\* have [the most robust syntax highlighting parser in the universe](http://pygments.org/) at its disposal. After casting about in vain for a pure PHP solution I had one of those "I wonder if there's a javascript lib for this" moments. Turns out there are a couple..

[Prismjs](http://prismjs.com/) is the lib I chose, primarily because of the well known pubs namedropped on the front page and the fact that it has my beloved Twilight theme right out of the box.

Installation was straightforward and took about 3 minutes following the instructions on the website.

The only trick is that by default, the Markdown parser wraps its code blocks with a class of `lang-$language`, and we need it to be `language-$language` for prism to correctly work its magic. Luckily this is exactly what that configuration item is for above.

## Conclusion

So that's it! Literally 10 minutes worth of work between "I wonder if there's a JS highlighter that I can use instead of trying to do this in pure PHP" and having this up and running.