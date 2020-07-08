---
layout: post
title: Remote Display Analyzer Logging with Login VSI
authors: [ryan]
comments: true
categories: [Remote Display Analyzer]
tags: [Login VSI, Remote Display Analyzer, Tools, Tools, VDI]
image: assets/images/posts/2018-08-29-remote-display-analyzer-logging-with-login-vsi/remote-display-analyzer-logging-with-login-vsi-feature-image.png
---
With the new release of Remote Display Analyzer, the logging functionality has been added. This is very valuable when performing user load tests with Login VSI. In this blog post, I will share how to integrate Remote Display Analyzer in a Login VSI workload.

<!--more-->
<h2>What is Remote Display Analyzer?</h2>
Remote Display Analyzer (RDA) is the tool to analyze the most common remoting protocols from Microsoft, Citrix, and VMware. It allows measuring the existing configuration and change settings on the fly. Using Remote Display Analyzer you are capable to achieve the best possible end-user experience for every user, on any device, anywhere.

In the latest release of the Remote Display Analyzer, the logging feature had been added!. This allows you to collect all the data in CSV format which can be analyzed afterward. Using the logging functionality in combination with a user simulation tool like Login VSI allows you to analyze the protocol impact under maximum capacity load. This way you can ensure the optimal user experience can be achieved even when the server is running at maximum capacity.

For more information about Remote Display Analyzer please visit the <a href="https://rdanalyzer.com/" target="_blank" rel="noopener">website</a>.
<h2>What is Login VSI?</h2>
Login VSI is the industry standard benchmark tool for virtual desktop environments and is used by many vendors. Login VSI simulates virtual users to generate a load on your virtual desktop environment. It's compatible with all the default vendors from Microsoft, Citrix, and VMware. If you want to know more about Login VSI, please visit their <a href="http://www.loginvsi.com" target="_blank" rel="noopener">website</a>.

<iframe width="800" height="461" src="https://www.youtube.com/embed/jSUKwDdBfz0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<h2>Adding Remote Display Analyzer to the workload</h2>
Login VSI uses a workload to execute actions so, adding Remote Display Analyzer to the workload allows each virtual user to collect the protocol data. It is recommended to start Remote Display Analyzer in the preparation phase as this is executed for each user. Make sure the Remote Display Analyzer executable is available for all the Login VSI users. In the following example, the default location is on the Login VSI share in a "_VSI_Tools\RDA" directory. The following line will start Remote Display Analyzer in logging mode.
<pre class="lang:default decode:true"># Start Remote Display Analyzer
VSI_ShellExecute("RDA", "%VSI_SHARE%\_VSI_Tools\RDA\RemoteDisplayAnalyzer.exe", "-logging %VSI_SHARE%\_VSI_LogFiles\%VSI_ACTIVETEST%\%VSI_ACTIVETEST%_RDA_%USERNAME%.csv -timing 10", "%VSI_SHARE%\VSI_Tools\RDA\")
</pre>
The command line will start Remote Display Analyzer in the logging mode. Using the "-timing" parameter you have the option to change the interval. A regular Login VSI workload is running for 48 minutes and by default, Remote Display Analyzer is reporting every second, which results in too much data for 48 minutes. In order to avoid a data overload, the sample above is configured with a 10-second interval. All the results will be saved to the active test located on the Login VSI share, including the specific username.
<h2>Results</h2>
Depending on the number of users you are testing with, there is going to be a lot of data. Each user will report a single CSV file with all the collected metrics. Some values may apears as null, this means the specified metric is not activated.

<a href="{{site.baseurl}}/assets/images/posts/2018-08-29-remote-display-analyzer-logging-with-login-vsi/rda-data-csv.png"><img class="alignnone wp-image-4707 size-large" src="{{site.baseurl}}/assets/images/posts/2018-08-29-remote-display-analyzer-logging-with-login-vsi/rda-data-csv.png" alt="" width="790" height="380" /></a>

The CSV format allows you to convert the data into a chart. The following example contains the maximum FPS limit and reported FPS during the Login VSI test.

<a href="{{site.baseurl}}/assets/images/posts/2018-08-29-remote-display-analyzer-logging-with-login-vsi/rda-data-chart.png"><img class="alignnone wp-image-4706 size-full" src="{{site.baseurl}}/assets/images/posts/2018-08-29-remote-display-analyzer-logging-with-login-vsi/rda-data-chart.png" alt="" width="958" height="358" /></a>
<h2>Conclusion</h2>
With the new release of Remote Display Analyzer, you are able to enable logging. This new feature it is possible to integrate it into a Login VSI workload which provides additional insights from a user perspective. This allows you to tweak with the protocol settings and see the impact of those changes. If you have any comments or questions, please leave them below.

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/kTHJb6pYsrY?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Malte Wingen</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
