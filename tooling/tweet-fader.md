---
layout: page
title: Tweet Fader
authors: [ryan]
comments: true
categories: [Tweet Fader]
---
<img class="alignleft size-thumbnail wp-image-1473" title="" src="{{site.baseurl}}/assets/images/pages/tweet-fader/twittericon.png" alt="" width="150" height="150" /><strong>Tweet Fader</strong>
Tweet Fader is the WordPress plugin to show your latest tweets on your WordPress site. The Tweet Fader plugin will add a widget that will display the tweets in the sidebar.

The Tweet Fader plugin uses a transition effect to display single tweets. It's a clean and simple look integrated in your WordPress theme. It is possible to enable the timestamp at the tweets that will update live. The timestamp is based on <a href="http://mattbradley.github.com/livestampjs/" target="_blank" rel="noopener">Livestamp.</a>

<a href="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-tweets.gif"><img class="alignleft size-full wp-image-3625" src="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-tweets.gif" alt="tweet-fader-tweets" width="276" height="156" /></a>

&nbsp;

<h2>How do I install the Tweet Fader plugin?</h2>
You can install Tweet Fader using the built in WordPress plugin installer. Use the add "Upload" option to select the Tweet Fader zip file. If you download Tweet Fader manually, make sure it is uploaded to "/wp-content/plugins/Tweet-Fader/".

Activate Tweet Fader in the "Plugins" admin panel using the "Activate" link.

Go to the "Appearance\Widgets" admin panel and add the Tweet Fader widget to the "Sidebar" or "Footer Area".

Now the Tweet Fader widget is ready to be configured.<a href="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-settings-page.png"><img class="alignright size-medium wp-image-3623" src="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-settings-page.png" alt="tweet-fader-settings-page" width="300" height="186" /></a>

Open the "Settings\Tweet Fader" admin panel to authorize Tweet Fader to access your Twitter. Click the Sign in with Twitter button to sign in. After you successfully signed in Twitter the admin panel will show You are signed in as: [twitter name]
<h2>Which settings can I set and which are required?</h2>
With the current version the following settings can be set:
<strong>
Widget header</strong> (optional):
The header text display above the widget.<a href="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-widget-settings-1.png"><img class="alignright size-medium wp-image-3624" src="{{site.baseurl}}/assets/images/pages/tweet-fader/tweet-fader-widget-settings-1.png" alt="tweet-fader-widget-settings" width="300" height="207" /></a>

&nbsp;

<strong>Number of tweets</strong> (required):
The total amount of tweets that will be displayed.

<strong>Interval between tweets in seconds</strong> (optional):
The amount of seconds until the next tweet will be displayed. When the option is not set it will be 10 seconds.

<strong>Show timestamp at tweet:</strong>
Show the timestamp at the tweets (by default disabled).

<strong>Open link in a new window/tab:</strong>
This will open each link in a new window or tab (by default disabled).
<h2>Where can I download the Tweet Fader plugin?</h2>
Download the plugin from Logitblog.com or Wordpress.org.

Download: <a title="Tweet Fader 1.3.0" href="{{site.baseurl}}/downloads/tweet-fader_2.0.0.zip" target="_blank" rel="noopener">Tweet Fader 2.0.0</a> @ Logitblog

Download: <a href="http://wordpress.org/extend/plugins/tweet-fader/" target="_blank" rel="noopener">Tweet Fader</a> @ Wordpress.org
<h2>Requirements:</h2>
Your theme must support widgets in order to use the Tweet Fader widget.

JQuery must be enabled in your theme. When all required Tweet Fader settings are configured and the tweets are not displayed, JQuery is probably not enabled in your theme. In order to enable JQuery try to add the following line in the "header.php" under the tag "&lt;head&gt;":
<blockquote>&lt;?php wp_enqueue_script('jquery'); ?&gt;</blockquote>
<h2>Change Log:</h2>
<ul>
 	<li><a title="Download Tweet Fader 0.1.0" href="{{site.baseurl}}/downloads/tweet-fader_0.1.0.zip" target="_blank" rel="noopener">Version 0.10
</a>First Release (Beta)</li>
 	<li><a title="Download Tweet Fader 1.0" href="{{site.baseurl}}/downloads/tweet-fader_1.0.zip" target="_blank" rel="noopener">Version 1.0
</a>Twitter API Update</li>
 	<li><a title="Download Tweet Fader 1.1" href="{{site.baseurl}}/downloads/tweet-fader_1.1.zip" target="_blank" rel="noopener">Version 1.1
</a>Character support (bug)</li>
 	<li><a title="Download Tweet Fader 1.1.1" href="{{site.baseurl}}/downloads/tweet-fader_1.1.1.zip" target="_blank" rel="noopener">Version 1.1.1
</a>Character support<a title="Download Tweet Fader 0.1.0" href="{{site.baseurl}}/downloads/tweet-fader_0.1.0.zip" target="_blank" rel="noopener">
</a></li>
 	<li><a title="Download Tweet Fader 1.1.2" href="{{site.baseurl}}/downloads/tweet-fader_1.1.2.zip" target="_blank" rel="noopener">Version 1.1.2</a>
Fixed a bug with the character "&amp;"</li>
 	<li><a title="Download Tweet Fader 1.2.0" href="{{site.baseurl}}/downloads/tweet-fader_1.2.0.zip" target="_blank" rel="noopener">Version 1.2.0</a>
Added timestamp option
Added Open link in new windows or tab</li>
 	<li><a title="Download Tweet Fader 1.2.1" href="{{site.baseurl}}/downloads/tweet-fader_1.2.1.zip" target="_blank" rel="noopener">Version 1.2.1</a>
Bug fix in the links</li>
 	<li><a title="Download Tweet Fader 1.3.0" href="{{site.baseurl}}/downloads/tweet-fader_1.3.0.zip" target="_blank" rel="noopener">Version 1.3.0</a>
Moved to Twitter API v1.1</li>
 	<li><a title="Download Tweet Fader 1.3.1" href="{{site.baseurl}}/downloads/tweet-fader_1.3.1.zip" target="_blank" rel="noopener">Version 1.3.1</a>
Resolved failed open stream bug
Resolved unknown characters in retweets</li>
 	<li><a href="{{site.baseurl}}/downloads/tweet-fader_2.0.0.zip" target="_blank" rel="noopener">Version 2.0.0</a>
Rebuild of Tweet Fader with TwitterOauth support to sign in Twitter
Settings page added to authorize with Twitter</li>
 	<li><a href="{{site.baseurl}}/downloads/tweet-fader_2.0.1.zip" target="_blank" rel="noopener">Version 2.0.1</a>
Removed cut-off urls from the tweet</li>
 	<li><a href="{{site.baseurl}}/downloads/tweet-fader_2.0.2.zip" target="_blank" rel="noopener">Version 2.0.2</a>
Resolved bug: cannot retrieve config and token</li>
</ul>
If you have any questions or problems please leave a comment or send a email to mail [at] logitblog.com.
