---
layout: post
title: "Why Memento moved from Java to CouchApps to Node.js Technology Stack"
date: 2012-09-27 19:53
comments: true
categories: ["node.js", "couchdb", "couchapps", "java"]
---
[Memento](http://mementodb.com), the app that I launched recently has already seen three technology stack changes. I started working on memento to be able to keep what I find on web with me as a personal memento. Being a Java architect I kicked off memento using Java. Since I was a solo developer on this project I wanted to keep everything lean in order to iterate quickly. This meant having an automatic code generation for the boilerplate code, using simple and powerful frameworks, following best practices, and tons of unit tests for making changes quickly and verifying the health of the app. 

## Spring Roo

With my lean strategy in mind I quickly used [**Spring Roo**](http://www.springsource.org/spring-roo), a rapid application development tool for Java developers to generate the complete app with front-end, back-end, authentication, persistence, logging, and unit tests within couple of hours. So far I was happy.

<!-- more -->

Now it was time to learn the magic tool and enhance the app. After hacking on Spring Roo for a couple of days I developed a good understanding of how it works. I absolutely loved the way it bootstrapped the complete app. It’s an absolute must-use tool for anyone who wants to bootstrap a Java web application. Though it has these great features, there are also some not-so-great ones. For front-end of the app, Spring Roo (spring-roo-1.1.4.RELEASE) used Dojo JavaScript framework, Spring MVC JSP tags, and JSPX technology. And the entire front-end generation was tied to the database model. This meant every time I made changes to the database model my user-interface was affected. After some initial hacking I came to an understanding that Spring Roo is not an ideal tool for managing the front-end. With this knowledge I decided to stop using it for managing the user interface changes. I was happy with this decision, as I wanted Memento to have an awesome user interface that I enjoyed using. And this would have required a lot of custom changes.

As the next step I started implementing the back-end logic for the memento. I decided that I will not develop any new logic without writing unit tests for it. These unit tests were really crucial for me because they allow you to discover problems quickly, and reduce manual testing time. After about two months I had a basic MVP (minimum viable product) ready and I wanted to host Memento on the web.

## VPS and EC2

I started with a VPS provider, [**Webfaction**](http://webfaction.com), for hosting Memento. After spending some time on configuring the VPS m/c and deploying Memento I was happy that memento was up and running. To my surprise, in the next one hour I go an email from the **WebFction** support that they have the killed the java process for my app as it was taking too much memory. This confused me since during the app development I never noticed any abnormal memory usage. This led to a realization that hosting a basic app on [Tomcat](http://tomcat.apache.org) or [Jetty](http://jetty.codehaus.org) servlet container required 256 MB of minimum RAM. In my opinion this is due to JVM overhead and th number of jars (58 jar files) that memento was loading in the JVM due to frameworks selected. This kicked VPS provider out of equation.

Next I decided to evaluate [Amazon EC2](http://aws.amazon.com/). For new customers Amazon EC2 provides a micro instance free for the first year. This meant no upfront cost to get started and no more memory constraints. After using Memento on EC2 for a month, I decided to host a continuous integration server, [Hudson](http://www.eclipse.org/hudson/). At this point I got another surprise. Whenever CI server started building memento on code commits entire EC2 instance would freeze. After some debugging I realized that's how Amazon wants a micro instance to perform. It defines a micro instance as- 

"Micro instances (t1.micro) provide a small amount of consistent CPU resources and allow you to increase CPU capacity in short bursts when additional cycles are available. They are well suited for lower throughput applications and web sites that require additional compute cycles periodically"

What this definition does not tell is that every time there is some constant CPU activity for 3 seconds, entire micro-instance will halt for next 6 seconds as it will not get any CPU cycle. This meant EC2 micro instances are not suitable for even light CPU intensive jobs. Once I figured out how CPU bursts work on a EC2 micro instance, I stopped using it as the CI server. 

My experience around memory and CPU utilization got me thinking about how to design memento for low resource utilization.

## Moving on to CouchDB and CouchApps

At the time I was searching for a solution to archive web pages. I did not want to store the web pages in SQL database as that would have caused the database to grow to pretty quickly with maintenance overhead. After some research I decided to use NoSQL data store [CouchDB](http://couchdb.apache.org) for storing web pages. I started hacking its **Map** **Reduce** views for querying data and came to know about CouchApps. 

Following is how [couchapp.org](http://couchapp.org) defines what are CouchApps

"CouchApps are JavaScript and HTML5 applications served directly from CouchDB. If you can fit your application into those constraints, then you get CouchDB's scalability and flexibility "for free" (and deploying your app is as simple as replicating it to the production server)."

My initial thought was that CouchApp would act as a lean 2-tier stack with no middle tier and a low resource utilization.I decided to rewrite the java version of memento as CouchApp. However, this technology stack change was not as smooth as I anticipated. It required learning the CouchApps technology and its tool chain for building/deploying the app. This obviously resulted in a tremendous learning effort. JavaScript, jQuery, Sammy.js, Compass, Sass, and Node.js were learnt. After 6 months the second version of the MVP was ready as CouchApp and now it was again time to host it on the web. 

This is when I started realizing the limitations of the CouchApps. Since, the app was hosted out of the CouchDB database I was forced into funky URLs (as dictated by CouchDB design documents), and weak authentication/security. There was limited community support for libraries and tool chains. 

## Node.js

The CouchApp tool, [kan.so](http://kan.so/) that I was using for building and deploying CouchApps introduced me to Node.js. **kan.so** used it for packaging and deploying CouchApps. In the process of rewriting [Memento](http://mementodb.com) as a CouchApp I hacked on some of the Node.js modules.  

Since I was again researching solutions CouchApp issues it occurred to me that Node.js could act as a thin layer between CouchDB and HTML5 GUI. Node.js is event oriented, asynchronous and has low memory and CPU utilization. Community support for the new features in the language itself and libraries/frameworks is fantastic. I was able to quickly iterate third version of MVP using Node.js within next three months and this is the version that is live in production today :-)

After working with Node.js technology stack I can now say with certainty that Node.js is my favorite language. It is the secret ingredient in Memento. Having said this Java still remains my first love.

## Conclusion

1. Java is good for developing apps and services when resource utilization is not an issue or if you want to write CPU intensive services.
2. CouchApps are good for rapid prototyping and for apps that do not have strict security requirements.
3. Node.js is even driven, light-weight and perfect langauge for developing next generation responsive applications.
