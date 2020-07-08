---
layout: post
title: No HTML5 videos on Windows Server 2016 RDSH
authors: [ryan]
comments: true
categories: [ Troubleshooting ]
tags: [HTML5, KB3192366, Login VSI, Troubleshooting, Windows Server 2016, Windows Updates]
image: assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/no-html5-videos-on-windows-server-2016-rdsh.png
---
Microsoft has released a technical evaluation version of Windows Server 2016 on the 26th of September 2016. This is the version of Windows Server 2016 which is going to be generally available mid-October this year. To get an idea of the scalability with Windows Server 2016, I deployed a couple Remote Desktop Session Hosts (RDSH). For some reason, the HTML5 videos were not playing. This blog post will take a closer look at the issue and how to solve this behavior.
<h2>HTML5 videos not playing behavior</h2>
In order to run the scalability tests, I'm using the well-known tool called Login VSI. Login VSI provides default websites that will play video content in Internet Explorer. When connected by a Remote Desktop Connection (RDP) the videos are not playing and stay black. When connected to the console on Hyper-V or VMware the videos are playing, which is very strange.

<a href="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/html5-video-not-playing.png"><img class="size-medium wp-image-3761 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/html5-video-not-playing.png" alt="html5-video-not-playing" width="300" height="187" /></a>
<h2>"Desktop Experience" feature not listed in Windows Server 2016</h2>
With Windows Server 2012 R2 it is known when creating an RDSH machine the feature "Desktop Experience" must be enabled in order to have media playback. As shown in the screenshot this feature is not listed in Windows Server 2016.

<a href="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/user-experiance-not-listed.png"><img class="size-medium wp-image-3763 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/user-experiance-not-listed.png" alt="user-experiance-not-listed" width="300" height="187" /></a>

When installing Windows Server 2016 with the full GUI by default the full "Desktop Experience" is included.
<h2> How to resolve HTML5 video issue</h2>
It turns out Microsoft has released a big critical update for Windows Server 2016 which is solving this behavior. Update <a href="http://catalog.update.microsoft.com/v7/site/ScopedViewInline.aspx?updateid=60600a8a-6479-43f7-8846-0bf0dc241f25">"Cumulative Update for Windows Server 2016 for x64-based Systems (KB3192366)"</a> released on the 24th of September, which is two days before the release of the evaluation edition of Windows Server 2016. This update will resolve the HTML5 playback issue and probably more. At this point the root cause of this behavior is unknown and there is little information regarding this update. When searching for the <a href="https://support.microsoft.com/en-us/kb/3192366">knowledge base article</a> I received a "This page doesn't exist" notification. When more information is available I will update this blog post.

<a href="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/kb-not-found.png"><img class="alignnone size-medium wp-image-3762" src="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/kb-not-found.png" alt="kb-not-found" width="300" height="199" /></a>

The update is listed in the <a href="http://catalog.update.microsoft.com/v7/site/ScopedViewInline.aspx?updateid=60600a8a-6479-43f7-8846-0bf0dc241f25">Windows catalog</a> and can be downloaded <a href="http://download.windowsupdate.com/d/msdownload/update/software/crup/2016/09/windows10.0-kb3192366-x64_af96b0015c04f5dcb186b879f07a31c32cf2e494.msu">here </a>or by Windows Updates.

After applying the update the HTML5 video will play as expected.

<a href="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/html5-video-playing.png"><img class="alignnone size-medium wp-image-3764" src="{{site.baseurl}}/assets/images/posts/2016-09-28-no-html5-videos-on-windows-server-2016-rdsh/html5-video-playing.png" alt="html5-video-playing" width="300" height="187" /></a>

<h2>Conclusion</h2>
Even when a new build of Windows Server 2016 is released it is very important to check for Windows Updates. In this case, the <a href="http://catalog.update.microsoft.com/v7/site/ScopedViewInline.aspx?updateid=60600a8a-6479-43f7-8846-0bf0dc241f25">KB3192366 </a>will solve the HTML5 video playback issues when connected with a Remote Desktop Connection (RDP) on a Remote Desktop Session Host (RDSH) machine. If you have any feedback please leave your comment below.

One final note at Microsoft, please make sure the knowledge base articles are available when releasing critical updates so we have information about resolved issues.
