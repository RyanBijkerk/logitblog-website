---
layout: post
title: How performance optimizations can break App-V
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, performance optimizations, Scripting, Troubleshooting, Windows 10]
image: assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/how-performance-optimizations-can-break-app-v-feature-image.png
---
Performance optimizations in VDI is a hot topic these days especially with the introduction of Windows 10. According to various sources there are major disk performance difference which effect the overall user experience. So this will result that performance optimizations in VDI become more important. But applying optimizations also has a down side. In this blog post I will share how performance optimizations can break App-V.

The following tweet displays the Disk Writes compared between a fully tuned Windows 7 VDI vs. fully tuned Windows 10. In order the support the same user experience moving to Windows 10 will ask more performance of you storage layer.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Compare <a href="https://twitter.com/hashtag/Windows7?src=hash&amp;ref_src=twsrc%5Etfw">#Windows7</a> and <a href="https://twitter.com/hashtag/Windows10?src=hash&amp;ref_src=twsrc%5Etfw">#Windows10</a> <a href="https://twitter.com/hashtag/VDI?src=hash&amp;ref_src=twsrc%5Etfw">#VDI</a> write IOPS; more <a href="https://t.co/kROy3sXrKW">https://t.co/kROy3sXrKW</a> &gt;&gt;&gt; <a href="https://twitter.com/hashtag/Storage?src=hash&amp;ref_src=twsrc%5Etfw">#Storage</a> <a href="https://twitter.com/hashtag/IOPS?src=hash&amp;ref_src=twsrc%5Etfw">#IOPS</a> <a href="https://twitter.com/hashtag/Latency?src=hash&amp;ref_src=twsrc%5Etfw">#Latency</a> <a href="https://t.co/wNd4rxrl0S">pic.twitter.com/wNd4rxrl0S</a></p>&mdash; Ruben Spruijt (@rspruijt) <a href="https://twitter.com/rspruijt/status/659499911608602624?ref_src=twsrc%5Etfw">October 28, 2015</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<h2>Default optimizations scripts and tools</h2>
There are many optimizations scripts online. Here you can find a couple of examples:

> https://github.com/cluberti/VDI/
> 
> http://www.computeraudiophile.com/f11-software/windows-10-optimization-script-community-effort-25361/
> 
> https://labs.vmware.com/flings/vmware-os-optimization-tool
> 
> https://www.vmguru.com/2013/05/vdi-optimization-script-part-1/
> 
> https://www.vmguru.com/2013/05/vdi-optimization-script-part-2/

The goal of these scripts are to disable functionalists within Windows which are not by default used. But it often happens these optimizations will break applications because of certain discrepancies. So beware this can break applications like App-V when applying these optimizations.

<h2>Getting to know the App-V dependencies</h2>
Microsoft App-V has a couple of dependencies on Windows services. In the scripts it is common to disable a lot of these Windows services including these used by App-V. By knowing the dependencies you can prevent that App-V will break. This can be done within the Windows Services Management Console by opening the properties of a services and opening the tab: Dependencies.

<a href="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-client-services-dependencies.png"><img class="alignnone wp-image-3410 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-client-services-dependencies.png" alt="App-v-client-services-dependencies" width="300" height="174" /></a>

These dependencies will also have dependencies so it could be a big tree with other services.

<a href="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-client-services-dependencies-tree.png"><img class="alignnone wp-image-3411 size-full" src="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-client-services-dependencies-tree.png" alt="App-v-client-services-dependencies-tree" width="400" height="462" /></a>
<h2>Symptom when depended services are disabled</h2>
In order to indicate if a depended services of the App-V client is disabled can be done by the following.
<ul>
 	<li>The most simple with the highest impact is that App-V packages won't be published and cannot be started.</li>
 	<li>Next to that within the Windows Event Viewer errors will be raised when a services cannot start.</li>
</ul>
<a href="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-eventlog-error.png"><img class="alignnone wp-image-3412 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-11-14-how-performance-optimizations-can-break-app-v/App-v-eventlog-error.png" alt="App-v-eventlog-error" width="300" height="211" /></a>

So after applying optimizations it could be useful to go through the Windows Event Viewer and search for errors.
<h2>Conclusion</h2>
Performance optimizations are important to get the best user experience in your VDI with the least amount of impact on your system, or keeping the current desktop capacity while moving to Windows 10. But these optimizations can break other applications which will impact the user experience. By understanding the dependencies of applications including the services you can prevent this bad experience.
