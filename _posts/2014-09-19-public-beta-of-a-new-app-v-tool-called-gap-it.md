---
layout: post
title: Public Beta of a new App-V tool called GAP-IT
date: 2014-09-19 21:47
author: Ryan
comments: true
categories: [Tools]
tags: [Microsoft, App-V, Beta, GAP-IT, Tool]
image: assets/images/posts/2014-09-19-public-beta-of-a-new-app-v-tool-called-gap-it/public-beta-of-a-new-app-v-tool-called-gap-it-feature-image.png
---
I'm proud to announce a new App-V tool called GAP-IT which stand for "Get App-V Publishing Times". GAP-IT can collect App-V publishing times in your environment and generate a Microsoft Excel report.

The report contains publishing times of individual packages, the percentage of published packages and the average publishing times. This is valuable information and can be used to verify if App-V publishing times are taking too long and if optimization is needed.
<h2>How does it work?</h2>
GAP-IT is collecting the publishing times remotely from the event log. Based on the date &amp; time of the written events the time of publishing is calculated. All this information is stored a local database. GAP-IT also has the capability to resolve the package name by using the API of the App-V Management Console but this functionality is optional.

With all this information a Microsoft Excel report can be generated which will visualize all the data like the example below.

<a href="{{site.baseurl}}/assets/images/posts/2014-09-19-public-beta-of-a-new-app-v-tool-called-gap-it/GAP-IT-report1.png"><img class="alignnone size-medium wp-image-2931" src="{{site.baseurl}}/assets/images/posts/2014-09-19-public-beta-of-a-new-app-v-tool-called-gap-it/GAP-IT-report1.png" alt="GAP-IT-report" width="300" height="194" /></a>
<h2>What do you need?</h2>
GAP-IT has a couple of requirement to work properly. A working App-V 5.0 streaming environment otherwise there is no data to collect and Microsoft Excel 2013 for the reporting.

More information can be found in the admin guide which is included in the beta package.
<h2>How can I get GAP-IT?</h2>
Do you want to try the GAP-IT beta in your environment? Enter your details in the contact form below and you will receive the details by email.

Beta is closed, you can find the download <a href="http://www.logitblog.com/tooling/gap-it" target="_blank">here</a>.

If you have comments or question please leave a comment below.
