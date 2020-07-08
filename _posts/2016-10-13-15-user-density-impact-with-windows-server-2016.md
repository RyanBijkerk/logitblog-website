---
layout: post
title: '15% user density impact with Windows Server 2016'
authors: [ryan]
comments: true
categories: ['Login VSI']
tags: ['#VDILIKEAPRO', Login VSI, Login VSI, Windows Server 2012, Windows Server 2016]
image: assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/15-user-density-impact-with-windows-server-2016-feature-image.png
---
Microsoft has released Windows Server 2016 on the 12th of October 2016. This means enterprises will look into upgrading their Remote Desktop environments. When updating an existing Remote Desktop Services (RDS) environment it is important to fully understand the performance impact to avoid scalability problems. This blog post is all about the performance results and the root cause of the decrease of 15% in user density with Windows Server 2016.
<blockquote><strong>Please note:</strong> This is a personal blog and not an official Login VSI blog post. The content used in this blog post is owned by Login VSI with the trademark #VDILIKEAPRO
For additional information please see the <a href="https://www.loginvsi.com/blog/678-server-2016-impact-on-vdi-user-experience">official blog post from Login VSI.</a></blockquote>
<h2>15% less user density with Windows Server 2016</h2>
Migrating to Windows Server 2016 will result in a user density decrease of 15% compared to the previous edition, Windows Server 2012 R2. These results are based on a clean installation of both Windows Server 2012 and Windows Server 2016 without any performance optimizations. The clean installation contains all required Login VSI applications including Microsoft Office 2016 and the required Windows roles and features.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-vsimax.png"><img class="alignnone wp-image-4045 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-vsimax.png" alt="server-2012-vs-server-2016-vsimax" width="649" height="320" /></a>

The best practice is to apply performance optimizations in a virtualized desktop scenario. As we know from previous publications these performance optimizations has a big impact on a VDI but not on Windows Server 2012 R2. A big difference with Windows Server 2016 is that default performance optimization does have a positive impact on the VSImax. The default performance optimizations will improve the user density with a 8%. The VMware OS Optimization Tool (OSOT) is used to optimize the operating system with the default selected profile.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2016-optimized-vsimax.png"><img class="alignnone wp-image-4046 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2016-optimized-vsimax.png" alt="server-2016-optimized-vsimax" width="649" height="320" /></a>
<h2>What is the root cause of the 15% impact?</h2>
The performance difference is caused by the amount of processes running on Windows Server 2016. With a default deployment, there are 10 more processes running which is causing more CPU utilization. So upgrading to Windows Server 2016 will require more CPU capacity.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-total-running-processes.png"><img class="alignnone wp-image-4050 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-total-running-processes.png" alt="server-2012-vs-server-2016-total-running-processes" width="649" height="320" /></a>

The difference in total amount of processes is visible in the CPU Utilization. When the operating system is in an idle state there is a 7% difference in utilization. Please note this is with a default deployment of both operating systems.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-cpu-running-processes.png"><img class="alignnone wp-image-4056 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-cpu-running-processes.png" alt="server-2012-vs-server-2016-cpu-running-processes" width="649" height="320" /></a>
<h2>CPU Core Utilization of Windows Server 2016</h2>
From a hypervisor perspective, it is clearly visible Windows Server 2016 has a higher CPU core utilization. The higher utilization is the main cause for the lower user density. As mentioned before this is because of the increase in the number of processes in Windows Server 2016.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-core-util.png"><img class="alignnone wp-image-4048 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-core-util.png" alt="server-2012-vs-server-2016-core-util" width="649" height="320" /></a>

The default performance optimizations result in a small improvement in the CPU core utilization. This improvement will increase the user density with a 9%.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2016-optimized-core-util.png"><img class="alignnone wp-image-4049 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2016-optimized-core-util.png" alt="server-2016-optimized-core-util" width="649" height="320" /></a>
<h2>Storage impact with Windows Server 2016</h2>
The footprint of Windows Server 2016 from a storage perspective is a little bit higher compared to Windows Server 2012 R2. The test environment of Login VSI's #VDILIKEAPRO is running on local SSD storage and therefore won't influence the VSImax score. There is one important note regarding storage. With the introduction of Windows Server 2016, Microsoft introduced Windows Defender default within the operating system. As we know from previous Project VRC publication anti-virus solutions could influence the overall performance.

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-reads-sec.png"><img class="alignnone wp-image-4052 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-reads-sec.png" alt="server-2012-vs-server-2016-reads-sec" width="649" height="320" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-writes-sec.png"><img class="alignnone wp-image-4051 size-full" src="{{site.baseurl}}/assets/images/posts/2016-10-13-15-user-density-impact-with-windows-server-2016/server-2012-vs-server-2016-writes-sec.png" alt="server-2012-vs-server-2016-writes-sec" width="649" height="320" /></a>
<h2>Conclusion</h2>
When upgrading to Windows Server 2016 the user density will decrease by 15%. In order to get the most out of current server capacity, performance optimizations are key. The performance difference is caused by the difference in running processes. There is a small difference from a storage perspective but the introduction of Windows Defender could have an impact in a production environment. Running these tests for Login VSI's #VDILIKEAPRO is always fun and if you have any questions regarding the results please leave a comment below.
