---
layout: post
title: Citrix ICA Visual Quality profiles research by ICT-R
authors: [ryan]
comments: true
categories: [Citrix]
tags: [Citrix, ICA, ICT-R, Visual Quality]
image: assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/citrix-ica-visual-quality-profiles-research-by-ictr-feature-image.png
---
Last week a new community platform called ICT-R published a detailed performance research regarding the Citrix ICA Visual Quality profiles. I decided to share some highlights from the <a href="https://www.ict-r.com/performance-difference-of-citrix-ica-visual-quality-profiles/" target="_blank" rel="noopener">research</a> in this blog post. For all the details I recommend to read the full research on <a href="https://www.ict-r.com" target="_blank" rel="noopener">ICT-R website.</a>

<!--more-->

There are many settings and optimization that can improve the overall performance and user experience. This is also the case for the ICA protocol as there are many options which affect the user experience. Within Citrix ICA there is a setting called Visual Quality. Which options are available and what is the effect of the various options? The research by ICT-R is focused on the performance difference between the Visual Quality settings within the Citrix ICA protocol.
<h2>What is ICT-R?</h2>
<a href="https://www.ict-r.com" target="_blank" rel="noopener">ICT-R</a> is an initiative of <a href="https://www.ict-partners.nl" target="_blank" rel="noopener">ICT-Partners</a> to enable their consultants to have a platform where they can show-off their skillset. Behind the site, ICT-R host a lab infrastructure where they can implement, test and reproduce almost any scenario and is used by colleagues to gather knowledge about new solutions within the IT market space.

The ultimate goal of ICT-R is to create an independent platform where IT enthusiasts, like us, can come and gather knowledge but are also able to share articles. ICT-R believes in sharing and that doesn’t go one way but is meant to be bi-directional. You are herewith invited to join our initiative and are more than welcome to share your experience, expertise, and opinion with us and the rest of the world.
<h2>Visual quality</h2>
This setting specifies the desired visual quality for images displayed on the user device. By default, this setting is Medium.
To specify the quality of images, choose one of the following options:
<ul>
 	<li>Low</li>
 	<li>Medium – Offers the best performance and bandwidth efficiency in most use cases</li>
 	<li>High – Recommended if you require visually lossless image quality</li>
 	<li>Build to lossless – Sends lossy images to the user device during periods of high network activity and lossless images after network activity reduces. This setting improves performance over bandwidth-constrained network connections</li>
 	<li>Always lossless – When preserving image data is vital, select Always lossless to ensure lossy data is never sent to the user device. For example, when displaying X-ray images where no loss of quality is acceptable.</li>
</ul>
Source: <a href="https://docs.citrix.com/en-us/xenapp-and-xendesktop/current-release/policies/reference/ica-policy-settings/visual-display-policy-settings.html" target="_blank" rel="noopener">https://docs.citrix.com/en-us/xenapp-and-xendesktop/current-release/policies/reference/ica-policy-settings/visual-display-policy-settings.html</a>
<h2>Conclusion</h2>
The Visual Quality Policy setting allows you to improve the quality of the image sent through the ICA protocol. The overall impact on capacity is none which means switching from a Low to High Visual Quality setting does not affect the overall user density on the servers.

<a href="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-loginvsi-vsimax.png"><img class="alignnone size-full wp-image-4716" src="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-loginvsi-vsimax.png" alt="" width="993" height="288" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-esx-cpu-util.png"><img class="alignnone size-full wp-image-4717" src="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-esx-cpu-util.png" alt="" width="993" height="288" /></a>

However, as there is no noticeable difference in the framerate it does have an effect on Round Trip Time and bandwidth. Using UDP result in less bandwidth send over the ICA protocol but during this research, the total traffic on the network adapter and the CPU utilization on the launcher (endpoint) increased. This is unexpected results and may require further investigation.

<a href="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-rda-bandwidth-compare.png"><img class="alignnone size-full wp-image-4719" src="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-rda-bandwidth-compare.png" alt="" width="993" height="288" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-rda-rtt-compare.png"><img class="alignnone size-full wp-image-4720" src="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-rda-rtt-compare.png" alt="" width="993" height="288" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-launcher-nic-kbps-compare.png"><img class="alignnone size-full wp-image-4718" src="{{site.baseurl}}/assets/images/posts/2018-09-20-citrix-ica-visual-quality-profiles-research-by-ict-r/visual-quality-launcher-nic-kbps-compare.png" alt="" width="993" height="288" /></a>

As some results are not as expected I hope this research provides a bit more insight into the performance impact of the Visual Quality Policy. If you have any comment or questions please leave them below.

For the complete research please visit the <a href="https://www.ict-r.com/performance-difference-of-citrix-ica-visual-quality-profiles/" target="_blank" rel="noopener">ICT-R site.</a> If you have any question or comments please leave them below.

Source: <a href="https://www.go-euc.com/performance-difference-of-citrix-ica-visual-quality-profiles/" target="_blank" rel="noopener">https://www.go-euc.com/performance-difference-of-citrix-ica-visual-quality-profiles/</a>

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/wgAf3-K94DQ?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Phil Botha</a> on <a href="https://unsplash.com/search/photos/blur?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Unsplash</a></span>
