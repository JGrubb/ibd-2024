---
title: Function 'fooController' is undefined
date: 2014-04-30
tags: 
  - javascript
  - angular
alias: 233-function-foocontroller-is-undefined
---

I spent about an hour pulling my hair over this one. I'm deploying an Angular to dev for the first time, it works fine locally, but everything is busted when I `grunt build` and push it up to a server. I'm using ngMin and using the supposedly safe syntax for defining all my dependencies, but unfortunately any Google search that includes "grunt build" and/or "minify angular" only turns up answer that pertain to that fairly well know problem.

So, I commented out Uglify in the build process and am still getting the error, only it's a lot easier to track down now, because my JS isn't minified. It blows up on the first one of my controllers that I wrote in Coffeescript, and is wrapped by Coffee's default function wrapper.

If this sounds like you, go to your Gruntfile and add an option to the coffeescript config ---

~~~ javascript
  coffee: {
    options: {
      sourceMap: true,
      sourceRoot: ''
    },
  }
~~~

becomes this ---

~~~ javascript
  coffee: {
    options: {
      sourceMap: true,
      sourceRoot: '',
      bare: true
    },
  }
~~~

Just make sure you're defining your scripts with (one of) the approved syntax(s) for keeping stuff out of the global scope - 

~~~ javascript
angular.module('fooApp').controller('fooController', function(){
 // stuff here
});
~~~