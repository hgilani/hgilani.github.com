---
layout: post
title: "Configuring a Vagrant VM as Chef Workstation: Part 2"
date: 2014-04-12 08:19
comments: true
categories: chef,knife,devops,rackspace 
---

In this part we would configure chef workstation that we created in [Part 1](http://himanshu.gilani.info/blog/2013/11/12/configuring-a-vagrant-vm-as-chef-workstation-part-1/) to provision nodes on the Rackspace cloud provider.

## Chef Terminology

Before we go over details of how to provision a Rackspace VM, lets review the chef terminology so that we have some common vocabulary 

* **Nodes**: Servers, VMs or physical machines or cloud instances.
* **Cookbook**: A cookbook is a collection of recipes, attributes, and templates that Chef uses to package, distribute, and share configuration details. A chef repository can have many cookbooks and typically a cookbook configures only one service.
* **Roles**: Roles are a way to group nodes by the function. Each role is associated with a run_list i.e. a set of cookbook/recipies that need to execute in given sequence for configuring the node. Nodes can have multiple roles.
* **Environments**: In chef different nodes could belong to different environments like dev, stage, prod, etc. Like roles environments also have a run_list. 
* **Data Bags**: Data bags are like cookbook attributes but are used to store sensitive information like passwords, api keys, secrets, etc.

<!-- more -->

## Setup Chef Server

Chef is a real powerful tool and comes in 2 different flavors at a high level i.e. chef server or chef solo. If you want to manage a single node then chef-solo is real simple and the way to go, but if you want to manage a set of nodes in a dynamic environment then chef-server is the way to go. Chef-serve tracks all provisioned nodes and manages chef repository i.e. cookbooks, roles, environments, etc. 

In this blog post we would use chef server hosted and managed by Opscode (makers of chef). They offer free service that could be used for managing 5 nodes. For setting up a chef-server account do following steps 

1. Sign up for an account at [https://manage.opscode.com/signup](https://manage.opscode.com/signup). 
2. Once you have created your account download your user key (USER.pem)
3. Create an Organization
4. Download Organization key (ORGANIZATION-validator.pem)
5. Download knife configuration(knife.rb) from the Organization page 

If you followed steps this far you would have following 3 files

1. {USERNAME}.pem
2. {ORGANIZATION}-validator.pem
3. knife.rb

**NOTE**: In file name above {USERNAME} and {ORGANIZATION} are placeholder for the user name and organization name on chef server.

If you get stuck then refer to [chef documentation](https://learnchef.opscode.com). 

## Setup Chef Repository (chef-repo) on your workstation

For setting up chef repository clone an empty chef repository project in `/vagrant` folder

```
cd /vagrant
git clone git://github.com/opscode/chef-repo.git
```

Now, create `.chef` folder inside the chef-repo folder and copy {USERNAME}.pem, {ORGANIZATION}-validator.pem and knife.rb that you downloaded. If you have followed this far then we would have following in /vagrant/chef-repo folder

```
chef-repo
|-- certificates
|-- .chef
|   |-- {USERNAME}.pem
|   |-- knife.rb
|   |-- {ORGANIZATION}-validator.pem
|-- chefignore
|-- config
|-- cookbooks
|-- data_bags
|-- environments
|-- roles

```

**NOTE**: Since we are using vagrant VM as chef-workstation we created chef-repo in /vagrant folder so that we do not loose chef-repo when we destroy vagrant VM

## knife

When we installed chef in [part 1](http://himanshu.gilani.info/blog/2013/11/12/configuring-a-vagrant-vm-as-chef-workstation-part-1/) it installed knife a command-line tool that provides an interface between a local chef-repo and the Chef server. Knife  manages nodes, roles, environments, data bags, cookbooks, etc on the chef server. In other words, it is a client of the chef server and it interacts with chef server using REST api. It is an essential tool when working with chef and we first need to configure our workspace so that we can use it.

### knife.rb location

Knife uses configuration in knife.rb file for working with chef server. The default location in which knife expects to find knife.rb file is `~/.chef/knife.rb`. Since we configured our chef-repo in `/vagrant` folder we either need to pass location of knife.rb file using --config option when executing knife commands (yes this needs to be done for every command as I did not find any configuration option for this) or execute all the knife commands from `/vagrant/chef-repo` folder.

### EDITOR environment variable	

Configure your favorite editor in EDITOR environment variable as some of the knife commands open up an editor so that you modify the roles, environments, data bags, etc in JSON format.

```
export EDITOR=vi
```

## Create a role for your node

Create an empty role file

```
knife role create base
```

*NOTE*: A role can have a run list of cookbooks or recipes that would be applied to a node. We are using an empty run list as a minimal example.

## knife-rackspace

Since we would be working with Rackspace cloud provider we need to install `knife-rackspace` gem so that we can manage nodes on Rackspace. To do this execute following command

```
gem install knife-rackspace
```

### Creating node on Rackspace

Since knife-rackspace plugin needs to provision nodes on Rackspace we need to configure Rackspace user and API key first. For this add following in `/vagrant/chef-repo/.chef/knife.rb` file

```
knife[:rackspace_api_username] = "Your Rackspace API username"
knife[:rackspace_api_key] = "Your Rackspace API Key"
knife[:rackspace_version] = 'v2'
```

**NOTE**: For production these values should come from environment variables and should not be checked into version control systems.

Now, we can use following command to provision a node in Rackspace

```
knife rackspace server create -r 'role[base]' --server-name mean --node-name mean --image 8a3a9f96-b997-46fd-b7a8-a9e740796ffd --flavor 2 --rackspace-region dfw
```

### Deleting node on Rackspace

First find the instance if of node you just create on Rackspace using command `knife rackspace server list` and then execute following command

``
knife rackspace server delete {instance_id} --purge
``

**NOTE**: For cleaning up nodes on chef-server use --purge in above command as otherwise you would need to execute following commands as well

```
knife node delete {node_name}
knife client delete {node_name}
```

**NOTE**: It is important to delete nodes on Rackspace once you are done testing as otherwise you could incurs costs for running servers on Rackspace.

For details on different command line options for knife-rackspace plugin refer to their [github repo.](https://github.com/opscode/knife-rackspace)

Follow me on twitter [here](http://twitter.com/hgilani)
