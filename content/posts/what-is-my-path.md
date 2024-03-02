---
title: What is my $PATH?
date: 2015-06-25
tags: 
  - devops
  - general development
alias: 234-what-is-my-path
---

I remember being very confused by this one early on. There were boatloads of tutorials on how to change your $PATH, but what that even means in the first place I just kinda had to figure out over the course of it all. It's actually pretty simple. Here's my attempt.

If you're coming from a Windows background, and you were in the habit of being really fussy about where you installed software on the hard drive, you may have just known how to fire up any old piece of software on your system. You navigated to the application in Windows Explorer and double clicked on it. It was really simple. That icon that you actually clicked on was the "executable", which is to say the file that starts the whole show.

Unix, Linux, and Mac systems also have executables. On a Mac, it's (represented by) the icon you click on to start the app. When you start getting deeper into development and start using the command line more, you're eventually going to come across some installation instructions that advise you to "update your path" for some reason. They usually give you a copy and paste thing to go along with it. But what does it mean?

Let me give you an example. Here's my path on this laptop right here --

~~~bash
MacPro-JGrubb 福 /usr/local/etc/ansible ➤ e0db473|master✓
10165 ± : echo $PATH [23h53m]
/usr/local/heroku/bin:/Users/jgrubb/.rbenv/bin:/Users/jgrubb/.rbenv/shims:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/X11/bin:/Users/jgrubb/.composer/vendor/bin
~~~

Now, never mind that there's a ton of garbage in there, and why is Heroku at the beginning of the path like that? I don't even remember doing that. Anyway, when you first start your computer up, or when you first start a shell session, your environment fires up and loads a bunch of configs. One of the configs that it loads is the list of places to look for the aforementioned executables. 

In a nutshell, your computer is going to look down that path from left to right. The different locations are separated by `:`, so that's a list of different locations on the computer that will be scanned to see that "that thing" is installed there. For instance - the MacIntosh comes preinstalled with Vim and Ruby. By default, stuff that comes with the OS is installed in `/usr/bin`. But, if you want a more recent version of Ruby, you might install it into `/usr/local/bin` (this is where Homebrew puts much of its stuff). If your path did not have `/usr/local/bin` before `/usr/bin`, you'd still be executing the system version of Ruby instead of the one you want. It's really simple, but again - took me a while.

So, how do you change it? Presuming you use Bash for a shell, you probably have a file called `~/.bashrc` or possibly `~/.bash_profile`. If you don't, you can safely create either of those files and put in a line like this --

~~~bash
export PATH=/usr/local/bin:/or/whatever/usually/bin:$PATH
~~~

This just says "hey, whatever my path is now, add those two directories to the beginning of it, and then assign that to be my new $PATH". 

Questions?