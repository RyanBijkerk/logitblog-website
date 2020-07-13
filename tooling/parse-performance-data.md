---
layout: page
title: Parse Performance Data
authors: [ryan]
comments: true
categories: [Parse Performance Data]
---
<img class="alignleft size-full wp-image-3065" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/08_settings.png" alt="" width="128" height="128" />Parse Performance Data (PPD) that quickly parses large performance CSV files and creates a Microsoft Excel report in a couple of seconds. This helps to collect the interesting metrics from the file and it even creates charts for each metric that is configured.
<h2>Where can I download it?</h2>

| Type | Version | MD5 Hash |
| :--: | :-----: | :------: | 
| Stable | [1.1.17188.1]({{site.baseurl}}/downloads/ppd/master/ppd.zip){:target="_blank"} | 659818b05236d0516ede1ef2806fc075 |
| Develop | [0.0.17125.3]({{site.baseurl}}/downloads/ppd/develop/ppd.zip){:target="_blank"}  | fc54f80cabdbe6d34f28f339166c3c25 | 
| Source code | [Github](https://github.com/RyanBijkerk/ppd){:target="_blank"} | N/A |

<h2>Requirements:</h2>
<ul>
 	<li>Microsoft Excel 2010, 2013 or 2016</li>
 	<li>.NET 4.0</li>
</ul>
<h2>How do I use PPD?</h2>
This section will describe how to use the tool.
<table>
<tbody>
<tr>
<td width="198"><strong>Description</strong></td>
<td width="403"><strong>Screenshot</strong></td>
</tr>
<tr>
<td width="198">Download the PPD.zip and extract the files.</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/parse-performance-data/extract-ppd.png"><img class="alignleft size-medium wp-image-3215" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/extract-ppd.png" alt="extract-ppd" width="300" height="215" /></a></td>
</tr>
<tr>
<td width="198">The Metrics.txt contains the default performance counter that will be collected. This can be extended with additional metrics.</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/parse-performance-data/metrics-ppd.png"><img class="alignleft size-medium wp-image-3216" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/metrics-ppd.png" alt="metrics-ppd" width="300" height="172" /></a></td>
</tr>
<tr>
<td width="198">Open a command prompt and run PPD.exe with the /f and the CSV file as parameter.</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/parse-performance-data/ppd-console-1.png" rel="attachment wp-att-3444"><img class="alignleft size-medium wp-image-3444" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/ppd-console-1.png" alt="ppd-console" width="300" height="152" /></a></td>
</tr>
<tr>
<td width="198">Within the same directory of the CSV file an Excel file is created.</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/parse-performance-data/ppd-explorer-1.png" rel="attachment wp-att-3445"><img class="alignleft size-medium wp-image-3445" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/ppd-explorer-1.png" alt="ppd-explorer" width="300" height="161" /></a></td>
</tr>
<tr>
<td width="198">The Excel sheet contains the metric and the chart.</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/parse-performance-data/PPD-Report.png"><img class="alignleft size-medium wp-image-3068" src="{{site.baseurl}}/assets/images/pages/parse-performance-data/PPD-Report.png" alt="PPD-Report" width="270" height="300" /></a></td>
</tr>
</tbody>
</table>
<h2>Feedback or ideas</h2>
Please share your feedback with me, I'm always looking for ways to improve the tool. Provide feedback in the comments below.
