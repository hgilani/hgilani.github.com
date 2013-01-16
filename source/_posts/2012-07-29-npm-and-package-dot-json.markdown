---
layout: post
title: "NPM and package.json"
date: 2012-07-29 09:28
comments: true
categories: [npm, node.js]
---

NPM is a package manager for node. Working with NPM is really simple. It gets installed as part of the node.js installation. NPM packages can be installed locally for a project or globally for the entire system. Whenever you install packages these packages are fetched from the [NPM registry](https://npmjs.org/). 

## Local Install

If you want to install any node package then you can simply do 

```
npm install {package_name}
```

This will install NPM package {package_name} in the current working directory. This is typically root of the project. 

## Global Install

At times you may want to have some packages installed globally in the system. Gloabl packages are not project specific. Any package can be installed globaly if you add the `-g` switch 

```
sudo npm install -g {package_name} 
```

Notice the use of `sudo` in the above command. This is because the package will be available globally in your system and you need admin privileges for making changes that affect the entire system.

## Local vs Global Install

When working with NPM packages you want to think in terms of local vs global packages. Local packages are project specific and need to be installed in project root folder. This way 2 different projects can use different version of the same package. 

### So when will you need global packages?

Global packages are useful when you need something for maintaining your project e.g. packages needed for building/testing your project. These packages typically do not get installed in the production environment. 

For a detailed description of different options available for npm command refer to [npm cheatsheet](http://blog.nodejitsu.com/npm-cheatsheet) 

## package.json

This is all good. Now we need a way to maintain dependencies for the project. This is typically done by creating a package.json at the root of the project and project dependencies in dependencies section. In addition to the project dependencies this file can contain project specific metadata like project name, description, version, author, script to execute for running/testing the application, etc. 

Following is a sample package.json file

```json
{
  "name": "memento",
  "description": "Memento: Discover and Save your Web",
  "subdomain": "memento",
  "domains": [
    "mementodb.com"
   ],
  "version": "0.0.1-77",
  "homepage": "http://mementodb.com",
  "author": "Himanshu Gilani <hgilani@gmail.com> (http://himanshu.gilani.info)",
  "private": true,
  "scripts": {
    "test": "mocha test/*-test.js",
    "start": "app.js"
  },
  "engines": {
    "node": "0.6.x"
  },
  "dependencies": {
    "mustache": "0.4.x",
    "request": "2.9.x",
    "express": "2.5.x",
   },
  "devDependencies": {
    "mocha": ">=1.0.3",
    "should": ">=0.6.3",
    "assert": "0.4.9",
    "jake": "0.2.33",
    "uglify-js": "1.2.6",
    "cloudfiles": "0.3.3",
  }
}
```   

Armed with this file anybody can get the bigger about the project. In addition to providing bigger it provides a standard way of maintaining node.js packages. Think of this like **Convention over Configuration** as when this file is available it prodvides following useful NPM commands

* Install all project specific dependencies - `npm install`
* Start the application - `npm start`
* Execute unit tests - `npm test`

Since executing these commands does not require any knowledge about the module. This is for the win.

For a good description of different properties that can be configured in package.json file refer to [package.json cheatsheet](http://package.json.nodejitsu.com) 

## Best practices

For best practices refer to following 2 blog posts

* [Package.json dependencies done right](http://blog.nodejitsu.com/package-dependencies-done-right)
* [Ten Lessons Learned Maintaining Nodejs Modules](http://blog.nodejitsu.com/ten-lessons-learned-maintaining-nodejs-modules)

Follow me on twitter [here](http://twitter.com/hgilani)
