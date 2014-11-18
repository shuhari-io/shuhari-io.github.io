---
layout: post
title: Uniting Angular and Rails API on Windows using Vagrant
---

## The Old World

On a previous project, we built our site using vanilla [Ruby on Rails](http://rubyonrails.org/). It worked pretty well in the beginning. Add a controller here, a view there, and add a few partials for good measure. Things began to get messy as we added in more JavaScript. At first it was a couple of event handlers and some Bootstrap JS components. Then we wanted to AJAXify the whole site, since we were keeping some data client-side. All hell broke loose, with JavaScript appearing all across the code base, including as returns to AJAX calls. It quickly became a huge mess.

Then there was the inevitable question of building apps to support our product. Now we'd need to build out an API on top of our existing code base. Luckily for me, we decided to transition to a new project (aka [Shuhari](http://shuhari.io)) before dealing with that task.

## Starting Fresh

I knew I wanted things to be different as I was setting up my development environment for Shuhari. I wanted to have a distinct API backend, not tied into any frontend. This way we can build an app at anytime without having to build a new API to support it. I wanted to keep our development and deployment simple, so hosting a JavaScript frontend from the same server as our API would be a plus.

I decided to stick with Rails and use [Rails API](https://github.com/rails-api/rails-api). I am already familiar with Rails, and if it's not broke why fix it? Additionally, separating the UI and the data service will make it easier to replace one without having to touch the other.

I chose [AngularJS](https://angularjs.org/) because I've always wanted to learn it, another project I'm working on uses it, and in the end why not? There are thousands of articles and arguments about choosing a JS framework, but I honestly think in the end you just pick one and you go.

So how do I get them to work together? Luckily my [Bing-fu](http://www.urbandictionary.com/define.php?term=Bing-Fu) was strong and I found a couple of great articles and repositories to help me out.

The best resource I found was [this blog](http://www.angularonrails.com/ruby-on-rails-angularjs-single-page-application/). I won't repeat everything from the post, you should read it and the other posts on the site which gave me tons of great info on using Angular and Rails together.


#
## Digging In

I should start by saying that my PC is a Windows machine. I like Windows for personal use and tolerate it for software development. For most web projects I use [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/) to run everything in an Ubuntu virtual machine. That way I can code and view my site in Windows, but still have everything running on Linux.

Unfortunately, Windows as my host machine proved to be the biggest obstacle here. The Angular setup provided in that post uses npm to handle dependencies. npm (no you don't capitalize it, even at the beginning of a sentence) makes a few assumptions in its implementation that don't play well with the Windows file system.

### Sharing Folders with a VM

When you boot a virtual machine using VirtualBox, it gets its own virtualized file system. It doesn't care about your host machine or its file system, VirtualBox does the hard work of interfacing between the two.

Unless you share a folder from your host machine, of course, as Vagrant does for you. My best understanding is that the folder is purely mounted on your host file system and the VM interfaces with it there.

#### Symbolic Links

When you do an `npm install`, npm makes heavy use of [symbolic links](http://en.wikipedia.org/wiki/Symbolic_link). By default, Windows does not allow users to create system links. So when npm tries to make them within the shared folder, you get errors about permissions issues.

There are three main ways to fix this. First, you can run `npm install --no-bin-links`. This tells npm not to use symlinks. This can cause unpredictable behavior though, since some npm modules may depend on the symlinks to function correctly or completely.

Second, you can run your Windows command prompt as an administrator. This allows you to create symlinks within Windows, and thus from your VM. But I don't wnat to do this every time I want to install a new npm module.

Finally, you can move the target directory out of the shared folder. This way npm installs on just the VM's file system. The problem is, our Angular project is in our shared directory, so we have to create a symlink from our Angular directory to the new target directory. Happily, you only need to create this symlink on VM creation, and it will persist throughout the life of the VM. That does mean we need to run our shell as an administrator for our first `vagrant up`.

#### Long Path Names

Another problem with npm and Windows is long path names. When installing, npm nests dependencies for modules within the directory for that module recursively. This leads to very long path names. [Windows limits path lengths to 260 characters](http://stackoverflow.com/a/265782/2490035). Please don't ask me why.

One option is to reduce the length of the path prepending node_modules, but that isn't a complete solution.

The only other option I've found is the same as the third option above for the symlink issue. We move the node_modules directory outside of the shared folder and create a symlink from the shared directory to the new target directory.

## Other Nuisances

Compass not working in Angular directory.

Change host in gruntfile

db template

CRLF issue with Postgres