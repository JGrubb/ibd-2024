---
title: Markdown in Rails 3 with RedCarpet
date: 2012-06-02
tags: 
  - rails
alias: 164-markdown-rails-3-redcarpet
---

**updated Jan 2014 for Rails 4, see bottom**

---

I just got this blog up and running yesterday, a marathon of pain but ultimately successful. So today I wanted to add some Markdown action so I didn't have to drop into TextMate to add `<p>` tags to everything all the time.


RedCarpet seemed to be the gem that was recommended, but it's recently seen a complete API overhaul that has rendered useless the vast majority of the de facto documentation out there. So, viewers of the [Markdown Railscast](http://railscasts.com/episodes/272-markdown-with-redcarpet), this one's for you.


It only involves a few changes, so if you're here it's likely that you know how to install it and you're here because of that "no 'new' method" error. Instead of 



`<%= Redcarpet.new(@post.body).to_html %>`

as mentioned in the Railscast, you'll need to do a little more setting up. This is how I did it.



~~~ruby
# app/helpers/application_helper.rb

def dat_markdown(text)
 markdown = Redcarpet::Markdown.new(Redcarpet::Render::HTML,
 :autolink => true, :space_after_headers => true, :no_intra_emphasis => true)
 markdown.render(text).html_safe
end
~~~

The first parameter is which renderer you want to use (either HTML or XHTML, for what?), and everything after that is your options glob which you can [get from here](https://github.com/tanoku/redcarpet). For readability you could also do -



~~~ruby
# app/helpers/application_helper.rb

def dat_markdown(text)
 options = {
 :autolink => true,
 :space_after_headers => true,
 :no_intra_emphasis => true
 }
 markdown = Redcarpet::Markdown.new(Redcarpet::Render::HTML, options)
 markdown.render(text).html_safe
end
~~~

After that all you have to do is 



`<%= dat_markdown(@post.body) %>`

and you're on your merry way. Enjoy.



#### January 2014 update

Rails 4 - check out [Kramdown](https://github.com/gettalong/kramdown). This is how easy it is now.

~~~ruby
# application_helper.rb

 def markdown_filter(text)
 Kramdown::Document.new(text).to_html.html_safe
 end
~~~

and `<%= markdown_filter @post.body %>`

Super bonus for the demographic reading this - syntax highlighting is included for free. See above. [Docs for the options hash are here.](http://kramdown.gettalong.org/options.html)