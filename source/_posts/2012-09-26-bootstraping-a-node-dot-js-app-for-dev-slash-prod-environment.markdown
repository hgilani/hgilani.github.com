---
layout: post
title: "Bootstraping a Node.js App for Dev/Prod Environment"
date: 2012-09-26 19:30
comments: true
categories: ["node.js", "express", "javascript"] 
---

Setting up a web server in node is really simple. A [basic hello world http server](http://nodeguide.com/beginner.html#a-hello-world-http-server) can be written in under 10 lines. However, if you want to write a production grade app then you are better of by using any framework that provides foundation and structure to your logic. When choosing frameworks there are no right or wrong answers. Important thing is to select a framework that is being actively developed, has community support and most of important of all you love the way it works. 

## Bootstrap a Node.js App with Express Application

[Express](http://expressjs.com) is a web application framework for Node.js. It is simple to use and can be configured to work with popular technologies. To understand how Express works you can follow the [Getting Started](http://expressjs.com/guide.html) page on the express web site. This guide also explains [how to use the express global executable](http://expressjs.com/guide.html#executable) for generating a skeleton web app. 

Once you have generated a skeleton web application. You need to understand how to structure the skeleton app for the Dev/Prod parity as well as making few changes to the app for making your app modular.

### Dev/Prod parity

You will notice following snippet in the skeleton app

```
app.configure('development', function(){
  app.use(express.errorHandler());
});
```
<!-- more -->
In the code `development` refers to the dev environment. Any dev specific setup that you want to do can be configured under this block. This snippet just configures a error handler for the dev environment. If you need to configure settings for any other environments like staging, production, etc then you can configure them the same way.

```
app.configure('production', function(){

  // production env. specific settings 

});
```
### process.env

In your express skeleton you will notice that port on which your app listens is configured as following

```
process.env.PORT
```

Now you may begin to think what is `process.env`. Node.js [documentation](http://nodejs.org/api/process.html#process_process) defines the `process` as a global object that can be accessed from anywhere. And `process.env` is an object that contains the user environment.

If you were running in bash shell and you have configured a `PORT` environment variable with value `80` then inside the app `process.env.PORT` will have the value 80. In the same vein you can configure a NODE_ENV environment variable that indicates to express in which environment it is executing.

On the production host you can execute

```
export port=80
export NODE_ENV=prodction
```

And on the development host you can let express use its default settings of running on port 3000 and set NODE_ENV to 'development'

### Simulating production environment during testing

If you ever want to simulate production environment for testing then that is easy. This can be easily done without configuring NODE_ENV variable in your environment. Instead of starting your app using `node app.js` you can start it as following

```  
NODE_ENV=production node app.js
```
This way NODE_ENV variable is only set for the current process execution and the value is not persisted in the environment settings.

### env.json

Setting environment variables to configure different settings for different environment is nice but it does not scale well. What we need is persistent storage for the environment properties that can act as a gold standard and is simple and easy to use. Meet **env.json**, add a new file in the project root with the name `env.json`. You can structure this file in the following way add key/value properties with right settings per environment.

```
{
	"development": {
		"facebook_app_id": "facebook_dummy_dev_app_id",
		"facebook_app_secret": "facebook_dummy_dev_app_secret",
	}, 
	"production": {
		"facebook_app_id": "facebook_dummy_prod_app_id",
		"facebook_app_secret": "facebook_dummy_prod_app_secret",
	}
```

This snippet shows how to configure same settings for development and production environment. If you ever has a need to add a new environment, then you can simply extend this file and add another property block.

Working with `env.json` is easy. In node parsing is a json file is as simple as loading the file using `require` statement. 

I like to abstract all the code that I can reuse as functions in `common.js ` file. So start by creating `common.js` file. Following is the parsing logic for env.json in this file.  

```
var env = require('env.json');

exports.config = function() {
	var node_env = process.env.NODE_ENV || 'development';
	return env[node_env];
};
```

Once you have created the common.js file using it for reading environment specific properties is as simple as loading it in app.js and using an object property. 

```
var common = require('./routes/common')
var config = common.config();

...

var facebook_app_id = config.facebook_app_id;
// do something with facebook_app_id

```

### Structuring express application with Open/Closed principle

After you have configured app.js correctly. You want to close it down from future modifications but you still need a way to extend it by adding new routes to the app. This is simple and easy to do. Read [A Simple Organization Scheme for Handling Routes in ExpressJS Apps](http://blog.pixelingene.com/2012/06/a-simple-organization-scheme-for-expressjs-apps/) for understanding how to do this by establishing some conventions for your project.

Follow me on twitter [here](http://twitter.com/hgilani)
