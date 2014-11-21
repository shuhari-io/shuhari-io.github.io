---
layout: post
title: Fixing Our Development Environment
---

## Digging In

I should start by saying that my PC is a Windows machine. I like Windows for personal use and tolerate it for software development. For most web projects I use [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/) to run everything in an Ubuntu virtual machine. That way I can code and view my site in Windows, but still have everything running on Linux.

Unfortunately, Windows as my host machine proved to be the biggest obstacle here. The Angular setup provided in that post uses npm to handle dependencies. npm (no you don't capitalize it, even at the beginning of a sentence) makes a few assumptions in its implementation that don't play well with the Windows file system.

## Sharing Folders with a VM

When you boot a virtual machine using VirtualBox, it gets its own virtualized file system. It doesn't care about your host machine or its file system, VirtualBox does the hard work of interfacing between the two.

Unless you share a folder from your host machine, of course, as Vagrant does for you. My best understanding is that the folder is purely mounted on your host file system and the VM interfaces with it there.

Vagrant automatically shares the folder it ran from with the VM as `/vagrant`. This is where our Rails and Angular projects live, and so it's where npm will install our frontend dependencies.

### Symbolic Links

When you do an `npm install`, npm makes heavy use of [symbolic links](http://en.wikipedia.org/wiki/Symbolic_link). By default, Windows does not allow users to create system links. So when npm tries to make them within the shared folder, you get errors about permissions issues.

There are three main ways to fix this. First, you can run `npm install --no-bin-links`. This tells npm not to use symlinks. This can cause unpredictable behavior though, since some npm modules may depend on the symlinks to function correctly or completely.

Second, you can run your Windows command prompt as an administrator. This allows you to create symlinks within Windows, and thus from your VM. But I don't want to do this every time I want to install a new npm module.

Finally, you can move the target directory out of the shared folder. This way npm installs on just the VM's file system. The problem is, our Angular project is in our shared directory, so we have to create a symlink from our Angular directory to the new target directory. Happily, you only need to create this symlink on VM creation, and it will persist throughout the life of the VM. That does mean we need to run our shell as an administrator for our first `vagrant up`.

### Long Path Names

Another problem with npm and Windows is long path names. When installing, npm nests dependencies for modules within the directory for that module recursively. This leads to very long path names. [Windows limits path lengths to 260 characters](http://stackoverflow.com/a/265782/2490035). Please don't ask me why.

One option is to reduce the length of the path prepending node_modules, but that isn't a complete solution.

The only other option I've found is the same as the third option above for the symlink issue. We move the node_modules directory outside of the shared folder and create a symlink from the shared directory to the new target directory.

## Progress

So far we've fixed both issues by moving the npm directory `node_modules` outside of the shared directory and creating a symbolic link to it. Here's what we add to our Vagrantfile to accomplish this.

```ruby
  $node_symlink_script = <<SCRIPT
mkdir /tmp/node_modules
ln -s /tmp/node_modules /vagrant/frontend/node_modules
SCRIPT

  config.vm.provision :shell, :inline => $node_symlink_script
```

You can see the rest of the Vagrantfile and the completed project at [this GitHub repository](https://github.com/brentax/vagrant-rails-api-angular). 

## Other Nuisances

Unfortunately, we're still not quite there yet. I ran into a few more hiccups and bugs before our development environment was fully ready. I'll talk about some more fixes in my next post.
