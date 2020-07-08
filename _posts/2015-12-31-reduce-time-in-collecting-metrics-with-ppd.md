---
layout: post
title: Reduce time in collecting metrics with PPD
authors: [ryan]
comments: true
categories: [Tools]
tags: [Citrix, Metrics, Microsoft, PPD, Tool, Tools, VDI, VMware]
image: assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/reduce-time-in-collecting-metrics-with-ppd-feature-image.png
---
Performance metrics are the most valuable metrics to have during performance testing or capacity testing of a VDI environment. If you talk with people that do a lot of these test they always recommend to collect extra data like VMware ESXtop or Microsoft Performance Monitor. But I encountered always a problem. These files are extreme large and that is a problem when opening with Microsoft Excel. In this blog post I'm going to explain how the tool PPD can help you reducing the time for collecting these important metrics.
<h2>The challenge to collect the metrics</h2>
As mentioned the files can be very large and in this example I have a VMware ESXtop file which is 60 MB with only data.

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/large-esxtop-file.png"><img class="alignnone wp-image-3432 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/large-esxtop-file.png" alt="large-esxtop-file" width="300" height="161" /></a>

When loading this large file is taking long and you can experience an error like this.

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/excel-error-esxtop-file.png"><img class="alignnone wp-image-3431 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/excel-error-esxtop-file.png" alt="excel-error-esxtop-file" width="300" height="115" /></a>

Next to that the headers are very difficult to read which makes it harder to collect the metrics you really need.

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/large-esxtop-file-not-to-read.png"><img class="alignnone wp-image-3433 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/large-esxtop-file-not-to-read.png" alt="large-esxtop-file-not-to-read" width="300" height="94" /></a>
<h2>The solution</h2>
So there are other tools but the most are not that quick or easy to use. Next to that I prefer to have the data in Excel so I can compare different runs. If you collect the metrics manually it takes like 10 minutes without making the charts. By using PPD it's a couple of seconds! And this includes making charts!

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-metrics.png"><img class="alignnone wp-image-3436 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-metrics.png" alt="ppd-metrics" width="300" height="173" /></a>

PPD comes with a text based file which contains the metrics you want to collect. Here you have the possibility to extend by added additional metrics which are valuable for you.

Running PPD is as simple as running a command which even can be automated of you like. Just run the command and use the parameter /f to specify the VMware csv file.

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-file-parse.png"><img class="alignnone wp-image-3435 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-file-parse.png" alt="ppd-file-parse" width="300" height="152" /></a>

As you can see this take only 9 seconds. The result is an Excel report which contains tabs with the specified metrics and an overall chart tab.

<a href="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-report.png"><img class="alignnone wp-image-3437 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-12-31-reduce-time-in-collecting-metrics-with-ppd/ppd-report.png" alt="ppd-report" width="300" height="272" /></a>
<h2>Conclusion</h2>
PPD is a small and powerful tool which can reduce a lot of time. When doing a lot of these performance test this tool is a must have in your default tool set. For more information about PPD check out this <a href="http://www.logitblog.com/tooling/parse-performance-data/" target="_blank">page</a>. If you have questions or comments please let me know.
