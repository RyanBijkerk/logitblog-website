---
layout: post
title: Tweet Fader 1.3.0 released
authors: [ryan]
comments: true
categories: [Tools]
tags: [API v1.1, Fade in, Fade out, JQuery, Tweet Fader, Twitter, WordPress]
image: assets/images/posts/2013-07-02-tweet-fader-1-3-0-released/tweet-fader-130-released-feature-image.png
---
Tweet Fader has updated to version 1.3.0 with Twitter API v1.1 support. In order to support the v1.1 API some requirements are added to the Tweet Fader plugin. Twitter decided to make authentication required and therefore I added the options to add specific keys from Twitter. My goal in the future is to improve this but for now you need to create an application with your Twitter account.

By creating an app on <a href="http://dev.twitter.com/apps" target="_blank">http://dev.twitter.com</a> you will get a consumer key, consumer key secret, access token and access token secret. With these keys you can provide Tweet Fader access to Twitter.

<a href="{{site.baseurl}}/assets/images/posts/2013-07-02-tweet-fader-1-3-0-released/screenshot-7-blug.png"><img class="alignnone size-medium wp-image-2385" src="{{site.baseurl}}/assets/images/posts/2013-07-02-tweet-fader-1-3-0-released/screenshot-7-blug.png" alt="" width="300" height="189" /></a>

You have to remember that these keys are not to be shared with others. For a full instruction to create a Twitter app please see the <a href="http://www.logitblog.com/?page_id=1673" target="_blank">Tweet Fader page</a>.

My goal for the future is to solve the authentication with the keys within the plugin. You will need to provide access to your Twitter account by a simple sign-in method. But I'm not a developer and it will take some time to create this and therefore I have chosen the current solution.

If you have any questions, comments or feedback just leave a comment below.