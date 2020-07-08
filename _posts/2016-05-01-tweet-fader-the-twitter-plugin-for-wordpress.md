---
layout: post
title: Tweet Fader the Twitter plugin for WordPress
authors: [ryan]
comments: true
categories: [Tools]
tags: [Tools, Tweet Fader, Twitter, WordPress]
image: assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-the-twitter-plugin-for-wordpress-feature-image.png
---
A new version of Tweet Fader has been released which is the Twitter plugin for WordPress. Tweet Fader is a WordPress plugin including a widget that displays your latest tweets on your WordPress site. It is fully functional with the 1.1 API version of Twitter. This blog post is about the changes in the new release and why I created the plugin.
<h2>My need for a Twitter plugin for WordPress</h2>
Twitter is for me a very important source of information which I consume daily. But next to consuming information it is also a good source of traffic for my blog. I saw at other blogs presenting their Twitter feed which I also wanted at my own blog. Showing a entire feed is to much information and displaying one tweet provides focus. Looking through the WordPress library did not fulfill my needs so why not creating it myself?

Without any experience in PHP nor creating something for WordPress I searched the internet and off I go. Following a couple of guides and I had created my first plugin for my own blog.

For me this is the Twitter plugin for WordPress, but maybe it is also for others. So why not sharing it with the WordPress community? Tweet Fader has been released on the WordPress platform 13th of October 2012.
<h2>The day Twitter had broken my plugin</h2>
Twitter was improving their API and introduced version 1.1. This new API included the requirement to be authenticated. Twitter has deprecated the 1.0 API at the 6th of November 2013 and this broke my and many other plugins. I'm using the plugin myself so it was not working on my blog anymore. I had no clue where to start and it took me a while to find a good guide that explained how to implement the authorization. I updated my plugin so others and I could use the plugin again. To be honest I was not happy with the implementation. The users of the Tweet Fader where forced to create an own Twitter application and provide the secretes in the widget. But what the heck, it worked right?
<h2>Room for improvement</h2>
While people where still using the plugin it was still bothering me I was sharing a plugin with a crappy authentication implementation. I knew there are other ways to authenticate where you authorize an app to use your Twitter account. Why could I not created that? After two years not maintaining the plugin I had decided to make a difference and start over. I started from scratch with one goal: Create the Twitter plugin for WordPress without the requirement of creating a Twitter application.
<h2>What is new in Tweet Fader?</h2>
As mentioned the plugin is completely rebuild and the requirement of creating a Twitter app has been removed.  A new settings page has been created where you can get authorized.

<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-setting.png"><img class="alignnone size-medium wp-image-3631" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-setting.png" alt="tweet-fader-setting" width="269" height="300" /></a>

When installing Tweet Fader the plugin needs to be authorized with your Twitter account. This can be done on the settings page with the "Sign in with Twitter" button.<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-settings-page.png"><img class="alignnone size-medium wp-image-3632" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-settings-page.png" alt="tweet-fader-settings-page" width="300" height="78" /></a>

When authorizing you will be redirected to the Twitter page where you can decide to allow  authorization or to cancel. Because Tweet Fader is only retrieving information read only permissions is only needed.

<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-auth.png"><img class="alignnone size-medium wp-image-3634" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-auth.png" alt="tweet-fader-auth" width="300" height="220" /></a>

When authorized successfully you will be redirected back to the settings page where it will display some information about the authorized account.

<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-settings-page-auth.png"><img class="alignnone size-medium wp-image-3633" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-settings-page-auth.png" alt="tweet-fader-settings-page-auth" width="300" height="98" /></a>

With the authorization in place 5 settings where removed from the widget which simplify the plugin in general.

These are the settings in the previous version:

<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/screenshot-8-blug.png"><img class="alignnone size-medium wp-image-2386" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/screenshot-8-blug.png" alt="screenshot-8-blug" width="157" height="300" /></a>

These are the settings in the current version:

<a href="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-widget-settings-1.png"><img class="alignnone size-medium wp-image-3624" src="{{site.baseurl}}/assets/images/posts/2016-05-01-tweet-fader-the-twitter-plugin-for-wordpress/tweet-fader-widget-settings-1.png" alt="tweet-fader-widget-settings" width="300" height="207" /></a>

As you can see Twitter name setting has been removed. Out of the box the authorized account Twitter feed is used within the widget.
<h2>Conclusion</h2>
With the major update I can really say that Tweet Fader is the Twitter plugin for WordPress. The changes in Tweet Fader improved the user experience and from the development point of view it will be easier to maintain the code. To download Tweet Fader please check out the <a href="https://wordpress.org/plugins/tweet-fader" target="_blank">WordPress site</a> or here on <a href="http://www.logitblog.com/tooling/tweet-fader" target="_blank">Logit Blog</a>. If you have feedback please leave a comment or drop me an email.
