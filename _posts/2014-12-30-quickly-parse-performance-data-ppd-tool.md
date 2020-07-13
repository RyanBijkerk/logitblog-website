---
layout: post
title: Quickly parse performance data with PPD Tool
authors: [ryan]
comments: true
categories: [Tools]
tags: [ESXtop, Microsoft, Perfmon, Performance, Tools, VMware]
image: assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/quickly-parse-performance-data-with-ppd-tool-feature-image.png
---
I often encounter issues with going through the large sets of performance data. Importing the large CSV files with powershell is taking very long. Therefore I created a tool called Parse Performance Data (PPD) that quickly parses large performance CSV files and creates a Microsoft Excel report in a couple of seconds. This helps to collect the interesting metrics from the file and it even creates charts for each metric that is configured.In this blog post I will show you more about this tool.
<h2>How does it work?</h2>
PPD is a console based application and can be used within a script. There are two main requirements:
<ul>
 	<li>.NET Framework 4.0</li>
 	<li>Microsoft Excel 2013</li>
</ul>
The collected metrics are specified in the Metrics.txt file, which is included in the download. This Metrics.txt file can be extended with other metrics like VMware ESXtop, Microsoft Performance Monitor or Citrix XenTop. By default metrics for VMware ESXtop and Microsoft Performance Monitor are included.

Any type of performance data can be parsed when the first column contains a date and time stamp, which is common in these kind of data.

PPD runs with one or more CSV files in the arguments and these files will be parsed.

<a href="{{site.baseurl}}/assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/PPD-Console.png"><img class=" size-medium wp-image-3066 alignnone" src="{{site.baseurl}}/assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/PPD-Console.png" alt="PPD-Console" width="300" height="95" /></a>
The report will be created in the same directory where the CSV file is located.
<img class=" size-medium wp-image-3067 alignnone" src="{{site.baseurl}}/assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/PPD-Explorer.png" alt="PPD-Explorer" width="300" height="124" />
<h2>What is the report like?</h2>
For each metric that is found an Excel worksheet is created where the time and results are stored. The time is based on the time difference between the first and second time stamp that is found in the CSV file. This way you are able to merge multiple parsed performance reports to compare the charts.

The Chart worksheet contains all the charts of the collected results. This way you quickly can see how the metric behaves.

<a href="{{site.baseurl}}/assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/PPD-Report.png"><img class="alignnone size-medium wp-image-3068" src="{{site.baseurl}}/assets/images/posts/2014-12-30-quickly-parse-performance-data-ppd-tool/PPD-Report.png" alt="PPD-Report" width="270" height="300" /></a>
<h2>Where can I download the tool?</h2>
I think this tool is very useful when you need to analyze performance data or write a whitepaper and need some charts. Therefore I decided to share this tool with the community and you can download PPD <a title="PPD Download" href="{{site.baseurl}}/tooling/parse-performance-data/" target="_blank">here</a>.

If you have comments or questions please leave them below.
