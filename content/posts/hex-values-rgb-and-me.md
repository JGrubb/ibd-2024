---
title: Hex values, rgb, and me
date: 2014-01-27
tags: 
  - ui
  - javascript
  - css
alias: 171-hex-values-rgb-and-me
---
One of the first discoveries I ever made in web development was that CSS hex values that were all the same number would always give me some shade of grey. #444 was dark, almost black. #ccc was light, perfect for form borders. Not long after I discovered that Photoshop wouldn't accept these values and would make me type in all 6 characters - #444444, #cccccc. 

Sometime after that I discovered how to tap into a shade somewhere between #eee and #fff, just a barely perceptible off-white with #f4f4f4. #f9f9f9 was lighter. #efefef was darker. The jobs at hand didn't really call for a deeper cognitive understanding. Just some off white colors.

---

It was only much more recently that somewhere in the corners of my mind I began to unravel the science, and just this morning walking down the stairs when it hit me in the way that I now have to write about.

#### parseInt()

There is a javascript function called `parseInt()`, and its job (in case it isn't clear) is to parse integers. There are two parameters - the first being the string/number you want to parse, but the second is often overlooked. The second is the "base number" in which you wish to parse, 10 (for decimal) is the default. I've read there is a bug whereby if you pass is a string beginning with the number 0, a base of 8 is assumed. This can lead to some weird bugs if that's not what you intend, so it's considered good style to always specify what you mean, usually 10. 

If you want to play with this function, pass it some hexadecimal (base 16) and see what you get back. This will let you do something like this --

~~~javascript
 parseInt('ef', 16); // -> 239
 
 parseInt('ff', 16); // -> 255
 
 parseInt('44', 16); // -> 68
 
 parseInt('45', 16); // -> 69
~~~

#### Hex and Rgb

Rgb color notation is pretty simple to get your head around. It means Red, Green, Blue. The same colors that your old tube TV used to put out in those tiny little dots if you got close enough. Mix those 3 colors together in the proper proportions anbd you can pull any shade of the rainbow out. Mix them together in equal portions and you get shades of grey, ie `rgb(239, 239, 239)` is a lovely shade of off-white. 

`16 x 16 = 256;` Duh.

rgb(68, 68, 68) === #444444;

I haven't really been required to think much in base 16, and the average developer probably isn't called on to think in those terms very often, but once that clicked for me, I can somewhat compute the shades of colors that I want in my head based off of thinking about basic color mixing theory and then doing some base 16 calculations in my head. Staving off Alzheimers, hopefully.