---
layout: 	post
title:  	"Fixing Chef version in Ubuntu precise64 box for Vagrant 2.0"
date:   	2013-11-15 13:00:00
categories: Tech
comments:	true
---

I upgraded [Vagrant](http://www.vagrantup.com/) on my workstation to version 2.0 a couple of weeks ago. Whenever I ran a project now, that I have worked on using Vagrant in the past, I was prompted with this message:

	There were warnings and/or errors while loading your Vagrantfile
	for the machine 'default'.

	Your Vagrantfile was written for an earlier version of Vagrant,
	and while Vagrant does the best it can to remain backwards
	compatible, there are some cases where things have changed
	significantly enough to warrant a message. These messages are
	shown below.

	Warnings:
	* `config.vm.customize` calls are VirtualBox-specific. If you're
	using any other provider, you'll have to use config.vm.provider in a
	v2 configuration block.

The message was telling that the `Vagrantfile` configuration file should be updated to a new schema, but for the time being the new version of Vagrant would be [backwards compatible](http://docs.vagrantup.com/v2/installation/backwards-compatibility.html) with that configuration.

Actually this turned out to be true. At least for some days. I use Chef to provision my development environment virtual machine in Vagrant. When running `vagrant up` on some of my projects now, cookbooks did not compile anymore. I thought this was an issue with a Ruby version that is installed wo the virtual machine. So it took some time of investigation and it turned out that actually Vagrant does not keep track on the upstream of Chef. The virtual machine did not run any recent version of Chef, but cookbooks keep track of the recent development and adopted new Chef schemas, at least the common ones.

In all the projects I work on that are powered by a Vagrant environment I use the Ubuntu precise 64 bit AMD Vagrant box (You can find out more about Vagrant boxes [here](www.vagrantbox.es)). That box I get from `http://files.vagrantup.com/precise64.box` and right now it boils down to `Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)`. That box currently runs Chef version 10.14.2. Actually the latest version of Chef, which I would expect the cookbooks to refer to, is 11.8.0.

Now there are several ways to make your box to take up with the latest version of Chef. You can include code in the Vagrantfile to do that 'manually' via aptitude or go vor any Ubuntu-specific package management tool. However, I found a very handy way of doing that on [StackOverflow](http://stackoverflow.com/questions/11325479/how-to-control-the-version-of-chef-that-vagrant-uses-to-provision-vms/18213542#18213542), which solved the problem easily. I used a Vagrant plugin called omnibus. To install the plugin on the host machine I simply ran `vagrant plugin install vagrant-omnibus`. To make my project's virtual machine use the latest version of Chef I included `config.omnibus.chef_version = :latest` before the provisioning specifications in the Vagrantfile.