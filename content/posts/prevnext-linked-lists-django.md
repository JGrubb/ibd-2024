---
title: Prev/Next as linked lists in Django
date: 2016-06-06
tags: 
  - general development
  - django
alias: 284-prevnext-linked-lists-django
---
## Problemspace

I want to be able to link a set of posts together in an order. If there is a next post relative to the one I'm on, I want a button to show up that says "next post" and links to it. If there is a previous post relative to the one that I'm on, I want a button that says "previous post" and links back to it. Pretty simple, conceptually. Basically I want to reproduce parts of the Drupal book.module as minimally as possible. 

So my first naive attempt was to add 2 [ForeignKey fields](https://docs.djangoproject.com/en/1.9/ref/models/fields/#django.db.models.ForeignKey) to the Post model - "previous" and "next".

~~~py
class Post(models.Model):

	title = models.CharField(max_length=255)
	body = models.TextField()
	summary = models.TextField(null=True, blank=True)
	slug = models.SlugField(max_length=255)
	pub_date = models.DateTimeField('Published at')
	published = models.BooleanField()
	tags = models.ManyToManyField(Tag)
	created = models.DateTimeField(auto_now_add=True)
	updated = models.DateTimeField(auto_now=True)
	previous_post = models.ForeignKey(
		'self',
		related_name='previous_post',
		blank=True,
		null=True,
		on_delete=None
	)
	next_post = models.ForeignKey(
		'self',
		related_name='next_post',
		blank=True,
		null=True,
		on_delete=None
	)
~~~

This worked on the front end but immediately raised a stink alarm, for a couple of reasons.

- You'd have to go and save this info twice for it to really work. Once on the current post and again on the referred post to link it back. == Workflow suck

- The truth about this ordering would be stored in two places, so it'd be really easy to mess something up and get out of sync.

This is essentially a [doubly-linked list](https://en.wikipedia.org/wiki/Doubly_linked_list) if you're keeping score, with the encumbant maintenance problems.

So I thought to perhaps override the `save()` method in order to hook into the operation and automatically populate the correct field on the referred item, but then of course, I'd have to do all kinds of gymnastics to watch for if that field were to be removed at some point and remove the corresponding field on the referred item, etc. I mean, it's a blog who gives a shit, but I've been doing this for long enough now that I can't help myself.

Another option in this same vein is to use the Django "signals" subsystem to hook into the same functionality, but the smell remains.

After coming home from DrupalCon it occurred to me that really all I need is the one pointer, since I should be able to derive the pointer back. I just had to figure out how to do it...

### `related_name()`

This is a pretty obvious use case - automatically deriving any pointers back to the current item. It just requires one extra DB query to ask "give me any items where the previous_post_id is this item's id".

The key is the [`related_name`](https://docs.djangoproject.com/en/1.9/ref/models/fields/#django.db.models.ForeignKey.related_name) argument to the model. 

I think this is automatically set for a normal ForeignKey field, but on models where the foreign key points back to the same model it's required. Judging from the docs, I was trying all manner of `post.post_set`, etc but it's actually just `post.previous_post`, which is counter-intuitive since what you're actually getting back from that is the "next" post. I chose to keep the "previous" field since you could just add the previous post as you're authoring the current one.

Current post model looks like this --

~~~py
class Post(models.Model):

	title = models.CharField(max_length=255)
	body = models.TextField()
	summary = models.TextField(null=True, blank=True)
	slug = models.SlugField(max_length=255)
	pub_date = models.DateTimeField('Published at')
	published = models.BooleanField()
	tags = models.ManyToManyField(Tag)
	created = models.DateTimeField(auto_now_add=True)
	updated = models.DateTimeField(auto_now=True)
	previous = models.OneToOneField(
		'self',
		related_name='previous_post',
		blank=True,
		null=True,
		on_delete=None
	)
~~~

And the prev/next fields look like this --

~~~html
{% if post.previous %}
 
[← previous: {{ post.previous.title }}]({% url )

{% endif %}
{% with next_post=post.previous_post %}
 {% if next_post %}
 
[next: {{ next_post.title }} →]({% url )


 {% endif %}
{% endwith %}
~~~

**note**

This might not technically be a linked list in the strictest sense, since a singly-linked list has pointers to the _next_ node in the chain. I've implemented it here as a "previous" pointer, since it makes more sense in the edit workflow. Since it makes more sense, hopefully we'll make more cents!

Stay tuned for the next episode where I decide that I'd like to have a Table of Contents and rip this whole thing out and do it over again.