---
title: A URL strategy
date: 2015-04-28
tags: 
  - general development
  - rails
  - django
alias: 258-a-url-strategy
---

I've got this decoupled CMS brewing in my head, and wanted to jot down what I think would be a kind of cool method for generating URLs and URL redirects and making sure everything stays in sync without having to maintain a giant table of redirects (ala Drupal).

The basic scheme would look like this -- `site.com/{item_type}/{item_id}/{item_slug}`. The type parameter could probably be optional, but it's there for now. An example URL could be `ignoredbydinoaurs.com/posts/123/the-totally-awesome-seo-juice`. 

Of course, in whatever framework you're working in, those url segments are going to be broken down into tokens passed into whatever controller function is running the query. So your query would/could look like this in the controller 

~~~rb

def show
	@post = Post.find(params[:id])
	if @post.slug != params[:slug]
	redirect_to "/#{@post.type}/#{post.id}/#{@post.slug}", :status => :moved_permanently
	end
end

~~~

This has the advantage of never going out of sync with a redirect table, and never opening up the possibility of having an alias and a redirect create a loop. This happens often in Drupal, so with this scheme, you're only looking up based on an item's ID, which should never change. If somehow a URL has made it out in the the wild that is not the preferred URL, nothing breaks, it just gracefully performs a redirect to the proper URL.

The only significant portion of the URL is the ID, everything else is decoration or SEO juice.

---

Somewhat off topic, but if you had a use cases where you were running multiple sites out of this CMS, and you had editors that frequently shared content, or wrote up each other's content for a sister site, then the primary key of of the article can stay consistent across different publications. 

"How would different editors utilize each other's content in that case? Like, how would different pubs have the same article with a different summary on this instance from that instance?"

PostgreSQL schemas, that's how. I'll write that up, probably sometime in 2017.

---

**2016, Django update**

This wasn't exactly hard to do, but I was surprised to find that nobody really wrote up how to simply issue a redirect like this in a Django view (controller to the rest of the world). Assuming that `get()` or `get_queryset()` was the answer, but I was wrong. [This was the help piece of info](https://docs.djangoproject.com/es/1.9/ref/class-based-views/generic-display/#detailview) that I needed. Did I mention how much I love Django's docs?

This is the method that drives this very page.

~~~python

class DetailView(generic.DetailView):
	model = Post
	
	def dispatch(self, request, *args, **kwargs):
	object = get_object_or_404(Post, pk=self.kwargs['pk'])
	if object.slug != self.kwargs['slug']:
	return redirect(object, permanent=True)
	# else, delegate up
	return super(DetailView, self).dispatch(request, *args, **kwargs)

~~~