---
title: Angular, Protractor tests, and Sauce Connect config
date: 2015-04-14
tags: 
  - testing
  - angular
alias: 257-angular-protractor-tests-and-sauce-connect-config
---

So it took me days of trying to piece together the correct config options to make my local protractor test suite run in a bunch of different setups on Sauce Labs.

~~~js
// An example configuration file.
exports.config = {

	sauceUser: 'jgrubb',
	sauceKey: 'fill-in-the-blank',
	sauceSeleniumAddress: 'localhost:4445/wd/hub',
	
	// Capabilities to be passed to the webdriver instance.
	// This option is called "capabilities" in the protractor docs
	// but whatever. this also works.
	multiCapabilities: [{
	// by default, these first two browsers will come up in 
	// Linux if you don't specify an OS
	'name': 'Chrome',
	'browserName': 'chrome'
	}, {
	'name': 'Firefox',
	'browserName': 'firefox'
	}, {
	'name': 'Win XP/IE8',
	'os': 'Windows XP',
	'browserName': 'internet explorer',
	'version': '8.0'
	}, {
	'name': 'Win7/IE8',
	'os': 'Windows 7',
	'browserName': 'internet explorer',
	'version': '8.0'
	}, {
	'name': 'Win7/IE9',
	'os': 'Windows 7',
	'browserName': 'internet explorer',
	'version': '9.0'
	}, {
	'name': 'Win8/IE10',
	'os': 'Windows 8',
	'browserName': 'internet explorer',
	'version': '10.0'
	}, {
	'name': 'Win8.1/IE11',
	'os': 'Windows 8.1',
	'browserName': 'internet explorer',
	'version': '11.0'
	}],
	
	// Spec patterns are relative to the current working directly when
	// protractor is called.
	specs: ['e2e/**/\*_spec.js'],
	
	// Options to be passed to Jasmine-node.
	jasmineNodeOpts: {
	showColors: true,
	defaultTimeoutInterval: 30000
	},

};
~~~

I burned tons of time just trying to figure out the correct names for the options, can't find where those are documented. This page - [https://docs.saucelabs.com/reference/platforms-configurator/#/](https://docs.saucelabs.com/reference/platforms-configurator/#/) turned out to be hugely helpful.

you probably figured this out already, but the Sauce Connect thing is awesome, and really easy to set up - [https://docs.saucelabs.com/reference/sauce-connect/](https://docs.saucelabs.com/reference/sauce-connect/)