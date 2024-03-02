---
title: Altering the Drupal Password Reset process
date: 2012-12-10
tags: 
  - drupal
alias: 40-altering-drupal-password-reset-process
---

### WARNING! D6 code ahead!!


As improved as D7 is, they don't seem to have caught on to this one, so maybe this will help those of you as well.

---


The internet is surprisingly scarce on information about this topic. Maybe most people don't need to alter the password retrieval process, but I've got a client with a variety of different roles on their site. These different roles have different profiles via the [Content Profile](http://drupal.org/project/content_profile "Content Profile | drupal.org") module. On account of numerous other account/profile management related modules that I've added - LoginToboggan, Auto Assign role, Invite - the whole process as well as the standard Drupal callbacks have gotten a little muddled. 


By muddled I mean broken. For instance, the standard user/%uid%/edit page is broken to hell. I don't know why really except that there's some competing permissions/access thing that I just can't, for the life of me, find. Unfortunately this is the page that the stock Drupal password reset process brings you to to reset your password after logging in.


"No sweat!", you might say. "Just call `hook_password_reset()` and be on your merry way!"


"I'd love to!", I'd say in reply. "But there is no `hook_password_reset()`!"


After much digging I finally found a clue to look in the user.module in a file called `user.pages.inc`. Herein lies the function that handles this very process - [`user_pass_reset()`](http://api.drupal.org/api/drupal/modules%21user%21user.pages.inc/function/user_pass_reset/6 "user_pass_reset | user.pages.inc | Drupal 6 | Drupal API"). Reading through this function is pretty cool. It's very readable, but also feels kind of bloated and hackish in a way that makes me feel a little better about how shitty a programmer I actually am.


Down around line 111 is where the magic happens. Now, if you're reading this, you've probably tried everything I tried. I tried setting `#redirect` on the form, which doesn't work because Drupal will ignore `#redirect` on the form if `#action` is set. I tried unsetting `#action` on the form, which breaks the whole thing because Drupal relies on that very unique URL being submitted to validate the password reset. All that logic can be viewed in `user_pass_reset`, so there's no monkeying with that. All you want to do is change that stinking URL inside that `drupal_goto()` and the end of that function! Has noone encountered this use case before?!?


So I did what any desperate soul would do. I hacked core. I didn't respect myself afterward, but at least the process worked like my client needed it to.


Step away, do some thinking. The comment at the top of the function says "menu callback for"...


### Enter `hook_menu_alter()`.


So obviously, the solution requires a custom module. In the absence of a `hook_password_reset()`, you have to move a little deeper down the chain. `hook_menu_alter()` is the backdoor into the club of elation that is having this project behind you. `hook_menu_alter()` lets you grab and *alter* any menu item in the whole process. Looking a little higher in the call chain on the User.module I take a peek inside `user_menu()`.



~~~php
$items['user/reset/%/%/%'] = array(
	'title' => 'Reset password', 
	'page callback' => 'drupal_get_form', 
	'page arguments' => array('user_pass_reset', 2, 3, 4), 
	'access callback' => TRUE, 
	'type' => MENU_CALLBACK, 
	'file' => 'user.pages.inc',
);
~~~

So let's copy that `user_pass_reset()` into our module (the *sweet_module* in this case) and rename it `sweet_module_user_pass_reset()`. Then let's define `my_menu_alter()` to be something like this - 



~~~php
function sweet_module_menu_alter(&$items) {
	$items['user/reset/%/%/%'] = array(
	'title' => 'Reset password',
	'page callback' => 'drupal_get_form',
	'page arguments' => array('sweet_module_user_pass_reset', 2, 3, 4),
	'access callback' => TRUE,
	'type' => MENU_CALLBACK,
}
~~~

Note the new function name for the callback, and the removal of the 'file' bit at the end. **Flush your menu cache** and you should now be hooking into that damn function finally.


What you do now is totally up to you, but you can hack away at the entire function until it behaves properly. In my case it was sending this type of user this way and that type of user that way. And nobody to the stock drupal user edit path. And you didn't hack core, and you can keep upgrading your site like you always did with the flick of a drush command without ever having to worry.


You're welcome.


