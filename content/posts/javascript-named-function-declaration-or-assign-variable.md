---
title: Javascript - named function declaration or assign to a variable?
date: 2013-07-20
tags: 
  - javascript
alias: 43-javascript-named-function-declaration-or-assign-variable
---
What's the difference between assigning an anonymous javascript function to a variable or just declaring a named function in the first place? Turns out "hoisting" of the function only works if you declare it as a named function in the first place. Assigning an anonymous function to a variable doesn't perform the hoist.

~~~js
(function() {
	console.log(f()); // 'hello'
	function f(){
		return 'hello';
	};
})();

(function() {
	console.log(f()); // undefined
	var f = function() {
		return 'hello';
	};
})();
~~~