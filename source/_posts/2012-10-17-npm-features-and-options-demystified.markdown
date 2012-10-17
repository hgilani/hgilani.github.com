---
layout: post
title: "NPM Features and Options Demystified"
date: 2012-10-17 16:58
comments: true
categories:["node.js","npm"] 
---
In the previous post, [NPM and package.json](http://himanshu.gilani.info/blog/2012/07/29/npm-and-package-dot-json/), I discussed how NPM and package.json could be used for managing the project and its dependencies. In this post I will discuss some of the overlooked features of NPM that help you in achieving more out of this tool

## npm install and package versions 

`npm install {package}` by default installs the package version tagged as "latest" in the npm registry. If you do not want the latest version of the package then you have few options. You can execute commands in the following way to control which package should be installed and from where to fetch the package. 

**Package with Specific Version:** `npm install {package}@{version}` 

```
npm install express@3.0.0
```

This command will install version 3.0.0 of express package form npm registry.

**Package in a Version Range:**  `npm install {package}@"{version range}"` 

```
npm install sax@">=0.1.0 <0.2.0"
```

This command will install sax package between version range 0.1.0 and 0.2.0

**Package with Specific Tag:** `npm install {package}@{tag}`

```
npm install passport@latest
```

This command will install passport package with latest tag.

**Private package:** `npm install {folder}` or `npm install {tarball file}` or `npm install {tarball url}`

```
npm install ./myPackage
npm install ./myPackage.tgz
npm install http://download.com/myPackage.tgz
```

**Package from Git Repository:** `npm install {git repo url}`

```
npm install git@github.com:hgilani/html2markdown.git
```

## Using npm install to also maintain package.json

When installing a new package, `npm install` could also update the package.json if you want. For updating package.json, you need to use one of these flags that indicate what kind of dependency you are installing

1. `--save`: Package will be added in the dependencies section of package.json
2. `--save-dev`: Package will be added in the devDepenctions section of package.json 
3. `--save-optional`: Package will be added in the optionalDepenctions section of package.json

## npm install or npm update

A lot of time you want to depend on the latest version of the packages so that you automatically get all the bug fixes and new features. If so then you should be able to update some or all the packages installed in your environment to the latest version. You should start by correctly configuring the versions properly in your package.json. For example, if you depend on version 2.0.0 of the express library then your package.json can say

```
  "dependencies": {
 		...
    "express": "2.0.0",
    	...
}
```

This tells NPM that you want to depend on a specific version of the package. This will keep things tight but it also makes upgrading to latest packages a manual effort. For ease of update, you should configure flexible versions in package.json that permit upgrading packages

```
  "dependencies": {
 		...
    "express": "2.x",
    	...
}
``` 

What this says is you want to depend on the latest express package with major version 2. With versions configured for easy upgrade, you can update to latest version of express package by executing either `npm install express` or `npm update express`

You can also update all packages by executing either `npm install` or `npm update`. For updating multiple packages you can execute `npm install {package1} {package2}` or `npm install {package1} {package2}`

### Difference between npm update and npm install

1. npm install will also update devDependencies. However, npm update will not
2. npm install will always reinstall the package. However, npm update will only update if the latest package is not installed.

In addition to upgrading package versions, `npm update` can also install any missing packages. In other words, it can function as `npm install` but the difference is that it will not install missing dev dependencies. 

## Speeding up npm install

In the blog post [NPM Tricks](http://www.devthought.com/2012/02/17/npm-tricks/), Guillermo Rauch has mentioned following two tricks that can help in speeding up npm install

```
npm install --production
npm install --loglevel warn
```

When you add `--production` option then npm will skip installing dev dependencies. In addition, when you add `--loglevel warn` then only useful information will be logged into console. 
 
## Some useful NPM commands

1. `npm list`: List installed npm packages with their dependencies. 
2. `npm outdated`: List all installed packages for which a latest version of package is available in npm registry.
3. `npm prune`: This command removes "extraneous" packages i.e. packages installed but not listed in package.json file.
4. `npm pack`: This commands creates an installable tarball from a package. This tarball can then be published on npm registry or installed in some other environment e.g. production.  
5. `npm help json`: If you ever want a reference documentation of different options you can configure in `package.json` file.

There are a lot more commands. To list all the commands you can execute `npm help` and for reading documentation on a specific command, you can execute `npm help {command}`

## .npmignore and .gitignore

`npm pack` is a useful command however sometimes you want to avoid packing some project resources in the tarball. For example if you are creating a package for installing in production environment then you will want to avoid packing unit tests in this package. This can be easily achieved by adding either `.npmignore` file or `.gitignore` file. Section ["Keeping files out of your package"](https://npmjs.org/doc/developers.html) in the "NPM Developer page" states following

> Use a .npmignorefile to keep stuff out of your package. If there's no .npmignore file, but there is a .gitignore file, then npm will ignore the stuff matched by the .gitignore file. If you want to include something that is excluded by your .gitignore file, you can create an empty .npmignore file to override it.

## Setup npm for tab completion

```
npm completion
```

Executing this command configures the tab completions into your current shell. It also outputs a shell script on command line that you can add to your ~/.bashrc or ~/.zshrcfile to make completions available for future shell sessions.