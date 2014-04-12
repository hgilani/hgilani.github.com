---
layout: post
title: "Scripting Google Analytics: Part 2 - Logging, Custom Variables & Traffic Sources"
date: 2013-03-03 21:19
comments: true
categories: analytics,google-analytics
---

In [part 1](http://himanshu.gilani.info/blog/2013/01/16/scripting-google-analytics-part-1-tracking-pages-and-events/) of this post I explained how to track page views and events in [Google Analytics](http://www.google.com/analytics/). In this post I will explain

1. How to use Google Analytics for logging data and javascript errors.
2. Custom Variables in Google Analytics.
3. How to use Google Analytics for track traffic sources.

<!-- more -->

## Logging data and Javascript Errors

Google Analytics events could be used for logging custom data and tracking Javascript errors in Google Analytics. For logging data just execute following 

``` javascript Logging Data
_gaq.push(['_trackEvent', 'INFO', 'Data to log...', url]);  
```

This mechanism could also be used to track error codes or messages received from AJAX calls or a REST service call. Following example assumes `response.code` contains the error code received in an AJAX call. 

``` javascript Logging Error Codes
_gaq.push(['_trackEvent', 'ERROR', response.code, url]);  
```

We could also use the same approach for tracking global javascript errors that are not handled.

``` javascript Tracking Javascript errors using Google Analytics
window.onerror = function(message, file, lineNumber) {
  	_gaq.push(['_trackEvent', 'Error', file + ':' + lineNumber, message + '']);
};
```

This snippet simply attaches a callback on the `onerror` callback of the `window` object for logging errors.  

## Custom Variables

Google Analytics is pretty good in tracking page views, but a lot of times we need a way to attach custom data to the page views. Custom variables in Google Analytics lets you do this. For setting a custom variable you could add following snippet

``` ruby Setting Up Custom Variables
_gaq._setCustomVar(index, name, value, scope)
```


In the above snippet, `_setCustomVar` has 4 arguments. 

* `index` argument is a slot or the position of the variable. Google Analytics only allows you track 5 variables per page page view and as a result this argument has a value between 1 to 5.
* `name` and `value` define the data that you are tracking. Think of it as **key - value** data associated with the current page view. 
* `scope` argument defines the duration for which Google Analytics should track this variable. It can take a value between 1 to 3 i.e. 1 (page level), 2 (session level) and 3 (visitor level)

    1. **Page** level scope means variable would be tracked only for the duration of current page view.
    2. **Session** level means variable applies to a single session or visit.
    3. **Visitor** level means variable would be tracked across sessions till users removes the Analytics cookie.

### Custom Variables gotchas

1. You can only set 5 variables per page view.
2. Do not change the variable names and slots (index) across page views. This will help in keeping things consistent.
3. Custom variable are tied to the current page view. So, any variable that you want to be tracked with the page view should be set before the `_trackPageview` call.

### Custom Variables uses

You might wonder what can I do with custom variables, well possibilities are endless. Once you start tracking some custom information then you could go in Google Analytics and do some custom analysis to understand how users behave with your website. Following are some examples of what to track using custom variables

1. Identify type of user  who viewed the page e.g. visitor, member, gold-user, platinum-user, etc
2. Track demographic information e.g. age, gender, etc of users who viewed the page.
3. Track how user arrived at the page e.g. paid search campaign, RSS, email newsletter, social n/w, etc
4. Track users who successfully shopped on your website.
5. Track users who contacted customer support.

## Track Traffic Sources

When the visitors start coming to your website then one of the metrics that you would want to track is who is referring users to your website. Users might have discovered your website through a search engine like Google, Yahoo, Bing, etc or might have come to your website by following a link from the newsletter you sent. But, if you want to track other sources then you need to format your inbound URL with following attributes

* `utm_source`: name of the traffic source e.g. bing.com, email newsletter, social, etc
* `utm_medium`: medium through which user came to your website e.g. cpc campaign, email, RSS, etc
* `utm_campaign`: string that lets you identify you campaigns e.g. bing_campaign_1, Spring 2013 newsletter, etc
* `utm_term`: keywords or tags that you want to associate with the source in Google Analytics 

Using these attributes an inbound URL in email newsletter would be formatted like

```
http://mementodb.com?utm_source=email_newsletter&utm_medium=email&utm_campaign=spring_newsletter
```

Google Analytics automatically tracks the traffic sources, keywords, and type of search i.e. organic or paid (Ad Words campaign) for all traffic coming from google.com What this means is that if you are using an Ad Words campaign then you don't need to do anything. But for traffic coming from Bing Ads it will not track the keywords and campaign details. Luckily this could be easily fixed but you would need to format the destination URL of the ads in Bing Ads account to use the `utm_source`, `utm_medium`, `utm_campaign`, and `utm_term` query parameters. If the bing campaign name is campaign_1 then this URL would be formatted as shown below

```
http://mementodb.com?utm_source=bing&utm_medium=cpc&utm_campaign=campaign_1&utm_term={QueryString}
```

In this URL pay attention to **{QueryString}** this should be entered exactly as shown with curly braces as Bind Ads platform will dynamically replace with search terms when the ad is served.

Follow me on twitter [here](http://twitter.com/hgilani)
