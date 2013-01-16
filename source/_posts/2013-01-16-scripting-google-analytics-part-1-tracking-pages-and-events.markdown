---
layout: post
title: "Scripting Google Analytics: Part 1 - Tracking Pages and Events"
date: 2013-01-16 11:31
comments: true
categories: ["analytics","google-analytics"] 
---
[Google Analytics](http://www.google.com/analytics) is a free web analytics tool from Google that you can use for tracking website statistics like page views, traffic sources, audience information, goals, funnels, etc. While a lot of the stuff is tracked automatically, it becomes overwhelming pretty quickly if you want to track customize analytics or want to track or create custom metrics. In this post, I will detail things I learned by experimenting with Google Analytics.

<!-- more -->

## Setup Google Analytics

If you are new to Google Analytics then head over to the [Google Analytics](http://www.google.com/analytics) website and create an account. Once you have created an account, you will be assigned a tracking code which will look like `UA-XXXXX-X`. For starting to capture analytics replace `UA-XXXXX-X` with the actual tracking code in the following code snippet and place it before the closing `</head>` element of the page where you want to track analytics. Ideally you want this code to execute on every page served on your web site.

``` ruby Getting Started with Google Analytics
<script type="text/javascript">

  var _gaq = _gaq || [];
  _gaq.push(['_setAccount', 'UA-XXXXX-X']);
  _gaq.push(['_trackPageview']);

  (function() {
    var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
    ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
    var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
  })();

</script>
```

The first line in this snippet initializes a JavaScript array variable `_gaq` on the page loads. You can think of this variable as a queue where you push events that you want to log in Google Analytics. In the next two lines we push two events to the tracking queue, first event(`_setAccount`) identifies the analytics account of your website and the second event(`_trackPageview`) tracks that page view i.e. some user visited the URL of the current web page. Rest of the lines dynamically add a `<script>` element to the web page for loading the Google Analytics library. 

So with this in place, every time a page is loaded in the browser a `_trackPageview` event will be recorded. Along with the page tracking Google analytics automatically tracks statistics on audience language and location, type of browsers and OS used, traffic sources, visitor flow, etc

## Tracking page views in AJAX or Single Page Websites

For the page driven websites where actions or clicks results in loading of new pages, Google Analytics will track the page views without configuring anything else. But this tracking does not work for the single page applications or AJAX enabled websites where first request to the server results in loading of the application and actions or clicks result in AJAX calls to fetch data from the server. It does not work because we only downloaded one page from the server and sent only one `_trackPageview` event. So in an AJAX application each AJAX request that updates the application view should be tracked as page view. This means we have to push the tracking event to the queue manually on each AJAX requests. Use following snippet for this

``` ruby Tracking page views for AJAX applications
_gaq.push(['_trackPageview', '{AJAX_REQUEST_LABEL}']);
```

In this snippet {AJAX_REQUEST_LABEL} is a string that you use to identify the request. If you are using a URL router like sammy.js that changes the hash of the page URL to reflect the current route then you can use following snippet for each route

``` ruby tracking page hash for sammy.js style URL routers
_gaq.push(['_trackPageview', window.location.hash]);
```

## Track Events

Besides tracking page views, you could also track events in Google Analytics. Events are a way to record user interaction and behavior on the page such as which signUp button was clicked on the landing page or which outbound link was clicked, if the user played the video or how far user scrolled down on the web page or did user started filling the credit card form and abandoned it half way. Possibilities are endless as it opens up a way to capture metrics that could allow you to optimize or redesign the pages.

Events can be tracked by simply pushing `_trackEvent` message to the analytics queue. `_trackEvent` has five properties associated with it as explained in the [Google Analytics Developer Guide](https://developers.google.com/analytics/devguides/collection/gajs/eventTrackerGuide)

1. Category (required): The name you supply for the group of objects you want to track. 
2. Action (required): A string that is uniquely paired with each category, and commonly used to define the type of user interaction for the web object.
3. Label (optional): An optional string to provide additional dimensions to the event data.
4. value (optional): An integer that you can use to provide numerical data about the user event.
5. non-interaction (optional): A boolean that when set to true, indicates that the event hit will not be used in bounce-rate calculation.

To track an even simply push `_trackEvent` message to the analytics queue.

``` ruby Tracking Events in Google Analytics
_gaq.push(['_trackEvent', 'category', 'action', 'label']);
```

The simplest use of the `_trackEvent` is to attach it to the DOM events such as `onClick`, `onScroll`, `onKeyup`, etc

``` ruby Tracking DOM events
<a href="http://mementodb.com" onclick="_gaq.push(['_trackEvent', 'category', 'action', 'label']);">Goto Memento</a>
```

This leaves an open question what should be the values of the `_trackEvent` properties. There are no specific values that you should or should not use. But to put things in perspective you could imagine category, action and labels as three dimensions of the event where category identifies the page, action identifies user activity on the page and label is anything helps you in tracking.To understand it clearly lets look at some examples

1. Which login button user click on the landing page?

``` ruby User clicked on Login button in header
_gaq.push(['_trackEvent', 'Landing-Page', 'Click', 'Login button in header']);
```

or 

``` ruby User clicked on Login button in body
_gaq.push(['_trackEvent', 'Landing-Page', 'Click', 'Login button in body']);
```

2. Which outbound link was clicked?

``` ruby User clicked on mementodb.com link
_gaq.push(['_trackEvent', 'outbound link', 'click', 'outbound link to mementodb.com']);
```

3. Which video user played?

``` ruby User played video with title "Linus Torvalds on Git"
_gaq.push(['_trackEvent', 'Videos', 'Play', 'Linus Torvalds on Git'])
```

4. Which version of software users are downloading?

``` ruby User downloaded Trial version of the software
_gaq.push(['_trackEvent', 'Downloads', 'Installer', 'Trial version']);
```

``` js User downloaded Pro version of the software
_gaq.push(['_trackEvent', 'Downloads', 'Installer', 'Pro version']);
```

In the next part of this post I will go over how to analyze the traffic sources, track errors, monitor how far users scroll on your web page

Follow me on twitter [here](http://twitter.com/hgilani)
