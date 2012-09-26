---
layout: post
title: "Why Memento moved from Java to CouchApps to Node.js Technology Stack]"
date: 2012-09-26 19:53
comments: true
categories: 
---
[Memento](http://mementodb.com) the app I launched recently has already seen three application stack changes. I started working on memento to solve the problem of keeping what I find on web with me as a personal memento. Being a Java architect at work I kicked off work on memento using Java stack. Since I was a lone developer on this project I wanted to keep everything lean so that I can iterate quickly. This meant having automatic code generation for the boilerplate code, using simple and powerful frameworks, following best practices and having tons of unit tests for making changes quickly and verifying the health of the app. 
## Spring Roo

With my lean strategy in mind I quickly used [**Spring Roo**](http://www.springsource.org/spring-roo), a rapid application development tool for Java developers, to generate the complete app with front-end, back-end, authentication, persistence, logging and unit tests within couple of hours. So far I was happy.

Now it was time to learn the magic tool and enhance the app. After hacking on Spring Roo for couple of days I developed good understanding of how it works. I absolutely loved the way it bootstrapped the complete app. It’s an absolute must use tool for anyone who wants to bootstrap a Java web application. But along with nice things you also get not so nice things. For front end of the app Spring Roo (spring-roo-1.1.4.RELEASE) used dojo JavaScript framework, Spring MVC JSP tags, and JSPX technology. And the entire front-end generation was tied to the database model. This meant every time I made changes to the database model my user-interface was affected. After some initial hacking I came to a conclusion that Spring Roo is not an ideal tool for managing the front-end. With this conclusion I decided to stop using it for managing the user interface changes. I was happy with this choice anyways because I realized that for me to use the application I need to love its user interface. And this would have any ways required a lot of custom changes.  

As the next step I started implementing the back-end logic for the memento. I decided that I will not develop new logic without having unit tests for it. Unit tests were really crucial for me as having unit tests allows you to discover problems quickly, faster troubleshooting and reduced manual testing time. After about two months of time I was at a point where I had a basic MVP ready and I wanted to host memento on the web.

## VPS and EC2

I started with a VPS provider, [**Webfaction**](http://webfaction.con), for hosting memento. After spending some time on configuring the VPS m/c and deploying memento I was happy that memento was up and running. For my surprise, in the next one hour I go an email from the **WebFction** support that they have the killed the java process for my web application as it was taking too much memory. This confused me as during application development I never felt that it was hogging memory. This lead to a realization that hosting a basic app on [Tomcat](http://tomcat.apache.org) or [Jetty](http://jetty.codehaus.org) servlet container required 256 MB of minimum RAM. In my opinion this is due to JVM overhead and th number of jars (58 jar files) that memento was loading in the JVM due to frameworks selected for the application. This kicked VPS provider out of equation.

Next I decided to evaluate [Amazon EC2](http://aws.amazon.com/). For new customers Amazon EC2 provides a micro instance free for the first year. This meant no upfront cost to get started and no more memory constraints. After using memento on EC2 for a month, I decide to host a continuous integration server, [Hudson](http://hudson-ci.org). At this point I got another surprise. Whenever CI server started building memento on code commit entire instance would freeze. After some debugging I realized that's how Amazon wants a micro instance to perform. Following is how it defines a micro instance

"Micro instances (t1.micro) provide a small amount of consistent CPU resources and allow you to increase CPU capacity in short bursts when additional cycles are available. They are well suited for lower throughput applications and web sites that require additional compute cycles periodically"

So every time there was some constant CPU activity for 3 seconds, entire instance would halt for next 6 seconds as it will not get any CPU cycle. This means EC2 is not suitable for even light CPU intensive jobs. Once I figured out how CPU bursts work on EC2 micro instance work I stopped using it as the CI server. 

My experience around memory and CPU utilization got me thinking about how to design memento for low resource utilization. 

## Moving on to CouchDB and CouchApps

By this time I was searching for a solution to store web pages for memento. I did not want to store the web pages in SQL database as that would have caused the database to grow to pretty quickly with maintenance overhead. After some research I decide to use NoSQL data store [CouchDB](http://couchdb.apache.org) for storing web pages. I started playing its map-reduce views for querying data and came to know about CouchApps. Following is how [couchapp.org](http://couchapp.org) defines what are CouchApps

"CouchApps are JavaScript and HTML5 applications served directly from CouchDB. If you can fit your application into those constraints, then you get CouchDB's scalability and flexibility "for free" (and deploying your app is as simple as replicating it to the production server)."
This felt like a lean stack with no middleware and I decide to let go of my investment in the java version of memento and started implementing it as CouchApp. However, migration was not as smooth as I expected. It required learning the CouchApps technology as well as its tool chain for building the application. This obviously resulted in tremendous learning on JavaScript, jQuery, Sammy.js, Compass, Sass, and Node.js. After spending about 6 months second version of the MVP was ready as CouchApp and now it was again time to host it on the web. 

This is when I started realizing the limitations of the CouchApps. Since, the application was hosted out of the CouchDB database I was forced into funky URLs (as dictated by CouchDB design documents), weak authentication/security. There was limited community support for libraries and tool chains. 

## Node.js

The CouchApp tool, [kan.so](http://kan.so/) that I was using for building and deploying CouchApps introduced me to Node.js. kan.so uses it for packaging and deploying coucpApps. During course of developing  CouchApp I hacked on some of the Node.js modules that it was packaging with the memento CouchApp. 

Since I was again searching for fixing CouchApp issues it occurred to me that Node.js could act as a thin layer between CouchDB and HTML5 application. Node.js is event oriented, asynchronous and has low memory and CPU utilization. Community support for the new features in the language itself and libraries/frameworks is fantastic. I was able to quickly iterate third version of MVP using Node.js within next three months and this is the version that is live in production today :-)

With my experience so far I can say for sure that Node.js is my favorite language and secret ingredient behind memento. But even with this reflection I still love Java as the language.
