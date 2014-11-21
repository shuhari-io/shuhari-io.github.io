---
layout: post
title: Uniting Angular and Rails API on Windows using Vagrant
---

## The Old World

On a previous project, we built our site using vanilla [Ruby on Rails](http://rubyonrails.org/). It worked pretty well in the beginning. Add a controller here, a view there, and add a few partials for good measure. Things began to get messy as we added in more JavaScript. At first it was a couple of event handlers and some Bootstrap JS components. Then we wanted to AJAXify the whole site, since we were keeping some data client-side. All hell broke loose, with JavaScript appearing all across the code base, including as returns to AJAX calls. It quickly became a huge, unmanageable mess.

Then there was the inevitable question of building apps to support our product. Now we'd need to build out an API on top of our existing code base. Luckily for me, we decided to transition to a new project (aka [Shuhari](http://shuhari.io)) before dealing with that task.

## Starting Fresh

I knew I wanted things to be different as I was setting up my development environment for Shuhari. I wanted to have a distinct API backend, not tied into any frontend. This way we can build an app at anytime without having to build a new API to support it. I wanted to keep our development and deployment simple, so hosting a JavaScript frontend from the same server as our API would be a plus.

I decided to stick with Rails and use [Rails API](https://github.com/rails-api/rails-api). I am already familiar with Rails, and if it's not broke why fix it? Additionally, separating the UI and the data service will make it easier to replace one without having to touch the other.

I chose [AngularJS](https://angularjs.org/) because I've always wanted to learn it, another project I'm working on uses it, and in the end why not? There are thousands of articles and arguments about choosing a JS framework, but I honestly think in the end you just pick one and you go.

So how do I get them to work together? Luckily my [Bing-fu](http://www.urbandictionary.com/define.php?term=Bing-Fu) was strong and I found a couple of great articles and repositories to help me out.

The best resource I found was [this blog](http://www.angularonrails.com/ruby-on-rails-angularjs-single-page-application/). I won't repeat everything from the post, you should read it and the other posts on the site which gave me tons of great info on using Angular and Rails together.

Unfortunately, this tutorial didn't work out of the box for me. This was no fault of the author, I'm just not on a Unix system! Fixing the bugs will be the topic of my next post.
