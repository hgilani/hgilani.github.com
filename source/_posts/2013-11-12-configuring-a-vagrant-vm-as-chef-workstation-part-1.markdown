---
layout: post
title: "Configuring a Vagrant VM as Chef Workstation: Part 1"
date: 2013-11-12 10:15
comments: true
categories: ["chef", "vagrant", "virtualbox", "vm", "ruby"] 
---

[Vagrant](http://vagrantup.com) is a powerful tool as it allows us to create "lightweight, reproducible, and portable" Virtual Machines (VM) on the developer machine using provisioning tools like chef and puppet. If you are using chef as the provisioning tool then you would already know that vagrant VM already has `chef-client` tool installed along with the version of ruby. Since the vagrant VM already has `chef-client` and `ruby` it should be straightforward to use any vagrant VM as a workstation for working the Hosted Chef server for provisioning and maintaining VMs in cloud services like AWS and Rackspace. However, I found this not to be the case and I realized that the process of configuring a chef workstation is rather convoluted as both the version of chef-client and its pre-requisite ruby are really old versions of the software that result in all sorts of errors on trying to use the latest chef plug-ins and services. In the end after a lot of trial and error I came to realize that before you can Vagrant VM as chef workstation you need to configure latest version of chef and ruby.

<!-- more -->

Following are the steps for configuring a vagrant VM as chef workstation. Chances are that you have already using and have both VirtualBox and Vagrant installed on your system. If this is the case, skip the first 2 steps and start with step 3. 

## Step 1: Install VirtualBox

VirtualBox is an open source virtualization application that allows you to create Virtual machines on Windows, Linux, Macintosh, and Solaris hosts. To get started install the latest version of VirtualBox. For this download and install the latest version for your OS from the [VirtualbBx download page.](https://www.virtualbox.org/wiki/Downloads). 

## Stpe 2: Install Vagrant

Vagrant is a command-line tool that can be used for provisioning and configuring VMs running in VirtualBox. For provisioning VMs it integrates with provisioning tools like chef and puppet. Vagrant also has a concept of `Vagrantfile` which uses ruby DSL and contains instructions on how a VM should be configured and provisioned. You can read more about Vagrant on its [website](http://vagrantup.com) which has excellent documentation. You can download and install the latest version of vagrant for your OS from the [Vagrant downloads page.](http://downloads.vagrantup.com)

**Note**: Vagrant also supports VMWare if you purchase a license. 

## Step 3: Create VM using following Vagrantfile

Lets start with a bare bones Vagrantfile. In the chef run-list you would see `apt` cookbook which is used to update the **Ubuntu** package repositories

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
 
  config.vm.provision :chef_solo do |chef| 
    chef.log_level = :debug    
    chef.cookbooks_path = "~/Dropbox/chef/cookbooks" 

    chef.add_recipe ("apt")
  end
end
```

## Step 4: Upgrade Ruby

First this we need to do is upgrade ruby to the latest version. For this I am using following cookbooks in my chef run-list

1. apt: Used to configure and manage the apt package repositories and proxy settings.
2. git: We would use git for managing cookbooks.
3. build-essential: This cookbook installs the tools and packages required for compiling software from source. 
4. yum: This cookbook configures the various yum components on Red Hat-like systems. Even though yum is not used on ubuntu this cookook is needed as it seems to be the pre-requisite for the rbenv cookbook.
5. ruby_build: Contains LWRP for the rbenv cookbook.

Note: You can download All the Cookbooks used in this post from my [cookbooks repo on github](https://github.com/hgilani/cookbooks)

With this run-list Vagrantfile would have following chef settings

```
  config.vm.provision :chef_solo do |chef| 
    chef.log_level = :debug    
    chef.cookbooks_path = "~/Dropbox/chef/cookbooks" 

    chef.add_recipe ("apt")   
    chef.add_recipe ("git")
    chef.add_recipe ("yum")
    chef.add_recipe "build-essential"
    chef.add_recipe "ruby_build"
    chef.add_recipe "rbenv::vagrant"
    chef.add_recipe "rbenv::user"

    chef.json = {
      'rbenv' => {
        'user_installs' => [
          {
            'user'    => 'vagrant',
            'rubies'  => ['2.0.0-p247'],
            'global'  => '2.0.0-p247',
            'gems'    => {
              '2.0.0-p247' => [
                { 'name'    => 'bundler' },
                { 'name'    => 'rails' },
                { 'name'    => 'haml' }
              ]
            }
          }
        ]
        }
      }
  end
```

Notice that in the `chef.json` block in the above code snippet we configured to install Ruby version `2.0.0-p247`. We also configured to install ruby gems `bundler`,`rails`, and `haml`. We would need these gems in our chef workstation later on when we want to install chef plug-ins for working with AWS and Rackspace.

## Step 5: Upgrade Chef

For upgrading chef you need to install the vagrant-omnibus plug-in. For these execute the following command in you host OS (do not do this in VM)

```
vagrant plugin install vagrant-omnibus
```

Once this plug-in is installed you can tell vagrant to upgrade to the latest version of chef by adding following line in the Vagranfile. 

```
config.omnibus.chef_version = :latest
```

If you have followed thus far following is you Vagrantfile

{% gist 7426779 %}

Running `vagrant provision` would now upgrade both ruby and chef to the latest version. 

In the next post I would discuss the steps for configuring chef workstation and we would provision a Rackspace VM.

Follow me on twitter [here](http://twitter.com/hgilani)
