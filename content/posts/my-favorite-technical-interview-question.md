---
title: My favorite technical interview question
date: 2015-02-13
tags:
  - general-development
aliases:
  - 253-my-favorite-technical-interview-question
---

It's very simple. I work at a [publishing company](http://www.advantagemedia.com/) in northern New Jersey. I think we hire pretty smart, but the technical interview tends to be more of a conversation about technology than a series of quizzes on the whiteboard. We've been hiring for a few positions lately, and I've recently hit upon the perfect conversational tech interview question. It's separated a lot of wheat from a lot of chaff for me in the past few months.

_"What text editor do you prefer?"_

---

I've recently begun using [PHPStorm](https://www.jetbrains.com/phpstorm/) after several years of bouncing between Vim for server side languages and TextMate for Javascript. I've had a license for PHPStorm for almost a year now, but never really got into it. In my younger days I guess I was kind of a hipster in that I thought I too cool for an IDE. Rather, I _wanted_ to be too cool for an IDE. IDEs were something you used if you worked with some bloated language like Java, or some compiled thing like Objective-C. If you used a slim, elegant, interpreted language like Ruby, you only needed a text editor. TextMate was the perfect little text editor for me for a number of years.

When I started here, we were still on Windows machines. Our entire Drupal stack was running on Ubuntu, and all of my background was in Linux and Mac, so it didn't make any sense to me to learn the tooling to work on Windows. So I downloaded VMWare Player and spun up my first Ubuntu 12.04 desktop VM. I taught myself to set up an intermediate LAMP/LEMP stack on this VM, and decided to start using Vim as my editor. I'd read about it plenty and most of the TM community seemed to be scattering to either Vim or Sublime. So Vim it was.

I became pretty proficient with Vim, but when I started playing Angular about this time last year, I was really missing a tree view. One of the killer-est plugins for Vim is [vim-rails](https://github.com/tpope/vim-rails), and the reason is actually because of the way that Rails is architected. It's very predictable if you go with the flow, and vim-rails let's you jump from controller to it's view to it's model to it's tests, over to any other model with a series of very simple and easy to learn shortcuts (the only kind I ever learn). I found myself really missing something like this with Angular, and just decided to go back to TextMate for Angular projects. TextMate has that fuzzy file search and a file tree explorer by default so it really made getting around an Angular project a little less tiresome. I know, I should have taken the opportunity to get to know Vim better, but I had (as you can imagine) a shitload of work to do.

So I spent several weeks back in TextMate. I'm vastly more productive in an editor with the Twilight theme, by the way. Those weeks go by and it's back to Drupal and Vim. My Vim chops have weakened by this point, and I've gotten pretty used to having the Twilight theme, which apparently didn't exist for Vim. [So I spent a day hacking an approximation together](https://github.com/JGrubb/vim-Twilight). It's also annoying to not have a file tree. I could install NerdTree, and tried to several times, but just never could figure out if I was learning a tool to get a job done, or just as some kind of test.

Enter my first project in Java a month ago.

---

We have some infrastructure here. It runs a lot of Java. This is a critical and most interesting piece of infrastructure, and we only have one Java guy here and he's pretty booked. Building web sites and writing javascript is fun and all, but underneath that is the layer of data in which my company lives and breathes. This is the layer for which this piece of infrastructure does the plumbing, therefore it has become much more interesting to me as of late.

This requires, obviously, learning a little bit about Java - something I'd stringently avoided for several years now. Java was old, Java was derided, Java could simply not be written without an IDE. These are prejudices which I no longer hold. I take this as a point of pride that I've risen to a level where I no longer define myself by my choice of technology. I've chosen enough of them at this point to see the merits of each for what they're for, as well as what they shouldn't be for. This is called experience, I think.

So anyway, my experience with Java and Eclipse is rather interesting. "Jump to declaration". Wow. A tree view not just of the files within my project, but of the methods within a file! Wow. It's at this point that I have to jump back into Drupal to do some plumbing between this and that. I fire up PHPStorm, thinking hey, this IDE thing is kinda cool. 

Lo and behold, many of the shortcuts in Eclipse are the exact same in PHPStorm. The menu and layout are very similar. I'm being productive in a tool which had previously only been sort of useful under come circumstances. I haven't even gotten to the debugger yet, on account of plumbing it into Vagrant and back being a pain in the ass. Just being able to reformat files and jump to function definitions is pretty nifty. 

Then I have to jump back into another Angular project. Hey, I think. PHPStorm is basically also [WebStorm](https://www.jetbrains.com/webstorm/), and therefore should be aware of Angular. It is! It indexes my entire project and within 30 minutes I'm being way more productive in PHPStorm on an Angular project than I would've been in TextMate. It's effing amazing! So I'm pretty much sold on PHPStorm, and then I notice that it doesn't support the little Sinatra/Sidekiq endpoint that goes along with this Angular project. I start thinking about IntelliJ a little bit, as I'm really starting to see the gains from being able to use one tool across many different technologies.

---

My point here is that I can go on for a ridiculously long time about my text editor and why. There's a story, and it also takes a winding path through what technologies I've used, why I used them, why I moved on or stayed with them. 

I don't care what editor you use. Ok, I kinda do actually, but I care much more that you have some opinions on the matter. If you don't, I'm not sure you really write code and that's kind of a problem for the types of technical positions that we are filling. These positions require a curious mind, one that likes to check out new things, and one that's ok with using old things if that's the right tool for the job.

If you're interviewing at ABM and you come across this blog post in doing some research about the team and the company, feel free to mention that. Taking the initiative to do some research on your potential teammates speaks much more to the kind of curious mind that you posses than a long winded discussion about your text editor ;).