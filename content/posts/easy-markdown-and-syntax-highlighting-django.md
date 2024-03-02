---
title: Easy markdown and syntax highlighting in Django
date: 2016-03-23
tags: 
  - python
  - django
alias: 275-easy-markdown-and-syntax-highlighting-django
---
Hi there, I'm new to Django. I love the contributed ecosystem, but all of the options that I found there for dealing with Markdown were just too heavy. I didn't need a Wysiwyg editor, I just wanted an output filter. As it turns out this is exceptionally easy to do!

---

Python has a really amazing lib situation, so I just found the smallest python Markdown lib that I could, it's called ["mistune"](https://mistune.readthedocs.org/en/latest/). Do a `pip install mistune`.

So within your app, let's call it "blog", create a directory called `templatetags`. By the way, this is all pretty easy to parse out of [their killer documentation](https://docs.djangoproject.com/en/1.9/howto/custom-template-tags/). Create a file in there called `markdownify.py`. 

~~~python
 # blog/templatetags/markdownify.py
from django import template
import mistune
 
register = template.library()
 
@register.filter
def markdown(value):
	markdown = mistune.Markdown()
	return markdown(value)

~~~

It is as simple as that. In whatever template you'll actually want to be rendering markdown, you'll need to include this templatetag with 

~~~python
 {% load markdownify %}
~~~

at the top of the template. Then you'll just pipe the output that you want to render like you do in every other template lib ---

~~~html
{{ post.body | markdown | safe }}
~~~

The full example of [the template that renders this page](https://github.com/JGrubb/django-blog/blob/master/blog/templates/blog/post_detail.html) is here.

---

### But wait, there's more!

How about syntax highlighting? We're programmers after all, and Python just happens to have the great-granddaddy of all syntax highlighting libs in [Pygments](http://pygments.org/). I've known of Pygments for years, since it used to be a requirement of one of the Ruby libs to Markdown rendering (if you wanted synta highlighting). In other words, even Ruby leaned on Pygments for a great number of years.

So `pip install pygments`. Then scroll down the page on the Mistune docs and follow along. You'll be adding some code to the `markdownify.py` file.

~~~python
from django import template
import mistune
from pygments import highlight
from pygments.lexers import get_lexer_by_name
from pygments.formatters import HtmlFormatter

register = template.Library()

class HighlightRenderer(mistune.Renderer):
    def block_code(self, code, lang):
        if not lang:
            return f"""
```
{mistune.escape(code)}
```
            """
        lexer = get_lexer_by_name(lang, stripall=True)
        formatter = HtmlFormatter()
        return highlight(code, lexer, formatter)

@register.filter
def markdown(value):
    renderer = HighlightRenderer()
    markdown = mistune.Markdown(renderer=renderer)
    return markdown(value)
~~~

That `HighlightRenderer` class is directly out of the Mistune docs, so thank you Mistune Author! That is seriously all it takes, but you'll need a stylesheet, of which there are plenty. I searched for "pygments stylesheets" and came across [this project](https://github.com/richleland/pygments-css), so you'll need to pick one of those themes and get it into your project somewhere. By default, the zenburn theme is expecting the wrapper div to have a CSS class of 'codehilite' instead of what it needs - 'highlight', so a quick search and replace and I had syntax highlighting in less than 5 minutes.

---

\*edit Sept 2016\*

So once you manage your way through all this, you'll be able to use "[fenced code blocks](https://help.github.com/articles/creating-and-highlighting-code-blocks/)" in your posts. They look like this --

~~~
```php
<?php 

function foo() {
 /// ...
}
```
~~~

becomes

```php
<?php 

function foo() {
 /// ...
}
```

You can use either a trio of tildes `~` or backticks \` to open and close one of those code blocks, and I typically just pass the file extension and it generally works. You can also write out the full name of the language.

~~~
```py
def method():
    return "foo"
```
~~~

becomes

```python
def method():
    return "foo"
```

Just be advised that it is possible to fatally hose your website if you happen to pass a language for which Pygments doesn't have a "lexer", meaning that it has no idea how to highlight the syntax of that language. That happened to me with some Varnish config files that I tried to highlight with a `.vcl` extension on them. I don't remember how I fixed it but I'm pretty sure it required going directly to the database to change the post since my site was toast. You are warned.