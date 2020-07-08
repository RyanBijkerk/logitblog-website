---
layout: post
title: Excel ChartStyles with C#
authors: [ryan]
comments: true
categories: [Development]
categories: [C#, Development, Microsoft Excel]
image: assets/images/posts/2014-07-05-excel-chartstyles-c/excel-chartstyles-with-c-feature-image.png
---
Which ChartSyles are available within Microsoft Excel? That was one of my questions when I was creating a tool that creates charts of data. But I couldn't find a list with all the available charts. It become even more strange when I checked the <a href="http://msdn.microsoft.com/en-us/library/microsoft.office.tools.excel.chart.chartstyle.aspx" target="_blank">class property</a> because it state: "An integer from 1 through 48 that represents the style of the chart." But I found a example online which was using the chart style 301, and that is clearly not between 1 and 48. Therefore I created an overview with all Microsoft Excel ChartStyle which can be used in C#.

Within the Excel overview a list with different ChartStyle are displayed. Each chart has the used ChartStyle in the title. The overview can be downloaded <a href="http://www.logitblog.com/downloads/ChartStyles.xlsx" target="_blank">here</a>.

<a href="{{site.baseurl}}/assets/images/posts/2014-07-05-excel-chartstyles-c/ChartStyleExample.png"><img class="alignnone wp-image-2850 size-full" src="{{site.baseurl}}/assets/images/posts/2014-07-05-excel-chartstyles-c/ChartStyleExample.png" alt="" width="670" height="334" /></a>

The following ChartStyles are available:
<ul>
 	<li> 1 until 48</li>
 	<li>301 until 352</li>
</ul>
Hopefully this can be useful to pick the right chart for your project. When you have a question or comment please leave them below.
