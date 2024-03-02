---
title: Parsing query string parameters out with javascript
date: 2014-01-30
tags: 
  - javascript
alias: 13-parsing-query-string-parameters-out-with-javascript
---

~~~javascript
// creates a global variable called urlParams
// adapt as needed.

// will forcefully downcase all query string params

// use --
// http://www.ignoredbydinosaurs.com?foo=bar&test=2
// urlParams.foo // bar
// urlParams.test // 2

window.urlParams = (function () {
var match,
pl = /\+/g, // Regex for replacing addition symbol with a space
search = /([^&=]+)=?([^&]\*)/g,
decode = function (s) { return decodeURIComponent(s.replace(pl, " ")); },
query = window.location.search.substring(1);

var params = {};
while (match = search.exec(query)) {
params[decode(match[1]).toLowerCase()] = decode(match[2]);
}

return params;
})();
~~~