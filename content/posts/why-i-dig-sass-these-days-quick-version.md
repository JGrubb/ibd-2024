---
title: Why I dig on Sass these days (the quick version)
date: 2011-07-08
tags:
  - css
aliases:
  - 168-why-i-dig-sass-these-days-quick-version
---

Sass is a programming language. It's purpose is to take some of the repetitive drudgery out of writing CSS (don't get me wrong, I love writing CSS). It does this by letting you define things such as constants and functions, things that other programming languages give you but CSS doesn't. The end purpose of Sass is to be digested and spit out as plain old CSS.

There are a couple of best parts. One of them is being able to define constants, referred to as variables in Sass. This lets you do things like define a standard color palate at the top of your sheet like this: 


```
$dark_blue: #064463
$green: #68db1e
$med_blue: #0089c7

```


Then, any time you need to use that shade of dark blue, rather than having to go find (or remember) `#064464` you just put `$dark_blue`. That's it. 


```
background: $dark_blue

```


Done.

Sass also has what are called mixins, but are more like functions in other languages. My personal favorite use of mixins is this one - 
```
@mixin gradient($color1, $color2)
  background: -webkit-gradient(linear, left top, left bottom, from($color1), to($color2))
  background: -moz-linear-gradient(270deg, $color1, $color2)
  background: linear-gradient(270deg, $color1, $color2)
  -pie-background: linear-gradient(270deg, $color1, $color2)
  behavior: url(/sites/default/files/pie/PIE.htc)

```


This defines all those different vendor specific ways of writing a CSS background gradient (with a dash of [css3pie](http://css3pie.com/) to boot!) and lets you simply drop this in your sheet :


```
@include gradient($dark_blue, $med_blue)

```


And again, done. It gets compiled and spit out exactly how you need it to be. Still experimenting with the exact colors of that gradient? Change the color once for the whole thing and you can't forget to do it for Firefox after you've spent 10 minutes getting it just right in Chrome.

It starts getting really fun when you do things like this :


```
background: lighten($dark_blue, 10%)

```


Oops, that's too light.


```
background: lighten($dark_blue, 7%)

```

*Perrrrfect*

Mix them all together!


```
@include gradient(lighten($dark_blue, 10%), $dark_blue)

```


This is how Sass saves you time. You can even try it out with existing stylesheets with the `sass-convert` command. The syntax is `sass-convert input-file.css`. This spits out your CSS file as Sass at standard output, so try this to get it into a file - 


```
sass-convert style.css > style.sass

```


Sass has a handy watcher function that you can invoke to automatically convert your Sass files into CSS files when you update them. Separate the Sass source file and the target CSS files by a colon.


```
sass --watch style.sass:style.css

```



Enjoy!

Edit 7/27 -- 

There are some damn handy additions in Sass 3.1. for instance, foreach loops - 


```

@mixin grad($color1, $color2)
  @each $browser in webkit, moz, o, ms
    background: -#{$browser}-linear-gradient(top, $color1, $color2)

```


- spits out this


```

          background: -webkit-linear-gradient(top, #e7e7e7, #b4b4b4);
          background: -moz-linear-gradient(top, #e7e7e7, #b4b4b4);
          background: -o-linear-gradient(top, #e7e7e7, #b4b4b4);
          background: -ms-linear-gradient(top, #e7e7e7, #b4b4b4);

```


So, there!