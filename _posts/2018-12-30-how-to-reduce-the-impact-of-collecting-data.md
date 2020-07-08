---
layout: post
title: How to reduce the impact of collecting data
authors: [ryan]
comments: true
categories: [Troubleshooting]
tags: [Citrix, ESXtop, Login VSI, Microsoft, Perfmon, RDA, Remote Display Analyzer, Troubleshooting, VMware]
image: assets/images/posts/2018-12-30-how-to-reduce-the-impact-of-collecting-data/how-to-reduce-the-impact-of-collecting-data-feature-image.png
---
When doing performance researches the data that is collected during the tests are very important to understand the impact. Without the data, you are not able to prove the real impact. In a recent ICT-R research an impact was noticed using Remote Display Analyzer (RDA) in SBC scenario. Collecting all the data from the different sources often comes with an impact and this blog post explains what you can do to reduce the impact.

<h2>Most common sources</h2>
The following sources are the most common which covers most of the VDI or SBC deployments.
<ul>
 	<li>VMware ESX - ESXtop;</li>
 	<li>Hyper-V - Perfmon;</li>
 	<li>XenServer - Xentop;</li>
 	<li>NVIDIA - nvidia-smi;</li>
 	<li>Windows - Perfmon;</li>
 	<li>Protocol - Remote Display Analyzer.</li>
</ul>
All those sources can be automated to capture and collect the data in raw CSV format using different methods. There are a couple posts on my blog that explains how to capture this using PowerShell.
<h2>Using the right interval</h2>
This is all depending on the source but they all have at least one thing in common, which is the interval. In all the sources you can adjust the interval which the data is captured. But adjusting the interval comes with a downside. A high interval results in very accurate results but will have more overhead and a lower interval will have less impact but reduces the accuracy of the data. The only way to determine the correct interval is to fully understand the metrics. For example, FPS is a fluctuating metric which requires a higher interval. If an interval of 30 seconds is configured and there is graphical content displayed for only 20 seconds there is a high chance this part is not captured. So, therefore, it is important to understand the metric and workload of the tests.
<h2>Applying filters</h2>
Sources like VMware ESXtop or Windows Perfmon have an information overload. Collecting all those details will have an impact and by only collecting the required information is a way to reduce the overhead. Most of the sources allow you to apply a filter or specify the sources that need to be collected. A downside of applying a filter is you may miss data from a specific source which can result in an incomplete or false conclusion. Therefore it is important to have a clear understanding and expectation of your tests.
<h2>Reduce user sources</h2>
In case of Remote Display Analyzer (RDA) you have the ability to collect the protocol data for each individual user. RDA used different methods to collect information including WMI which require resources. In an SBC scenario, the impact will be higher compared to VDI as multiple users will be on a single VM sharing the resources. Also with RDA you have the ability to increase the interval but another way to reduce the impact is to collect data from an individual user. In the case of the researches on ICT-R compares are always done on a single user. In an SBC scenario, it may be interesting to collect the data from each individual host. This requires some scripting but can be done for sure (new post incoming!).
<h2>Conclusion</h2>
Collecting performance data will have an impact. Without data your just another person with an opinion, so it is on the schedule for ICT-R to do proper research. There are multiple ways to reduce the impact but for all sources, it is important to understand the workload you are testing. This way you are not missing valuable information which may affect the conclusion. If you have a suggestion to reduce the impact please let me know in the comment below.

&nbsp;

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/MldQeWmF2_g?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Christa Dodoo</a> on <a href="https://unsplash.com/search/photos/paper?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Unsplash</a></span>

&nbsp;
