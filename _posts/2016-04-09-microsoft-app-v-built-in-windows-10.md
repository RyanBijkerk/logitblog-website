---
layout: post
title: Built-in Microsoft App-V in Windows 10
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, Windows 10]
image: assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/built-in-microsoft-app-v-in-windows-10-feature-image.png
---
It is known that Microsoft is going to integrate their application virtualization technology named Microsoft App-V within Windows 10. The integration is scheduled in the upcoming Redstone release for later this year. In the Insider Preview Windows 10 build 14316 the binaries of App-V are now by default available. This blog post wil take a closer look in the built-in Microsoft App-V Client.

<h2>Microsoft App-V binaries</h2>
As you can see on the following screenshot the binaries of Microsoft App-V are located within the directory location: "C:\Windows\System32\"

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-binaries.png" rel="attachment wp-att-3588"><img class="alignnone size-medium wp-image-3588" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-binaries.png" alt="appv-binaries" width="300" height="169" /></a>

Also the Microsoft App-V Client Services is by default installed. Please note the services is disabled by default.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-client-service.png" rel="attachment wp-att-3589"><img class="alignnone size-medium wp-image-3589" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-client-service.png" alt="appv-client-service" width="300" height="92" /></a>

It is not clear which version of App-V is integrated in this build of Windows 10. The version can not be detected because the properties of the AppVClient.exe are not set. Within the refistry the client version states 10.0.14316.1000 which is the Windows build version.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-client-version.png" rel="attachment wp-att-3599"><img class="alignnone size-medium wp-image-3599" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-client-version.png" alt="appv-client-version" width="239" height="300" /></a>
<h2>App-V PowerShell commands</h2>
As expected the App-V PowerShell commands are available.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-powershell-commands.png" rel="attachment wp-att-3597"><img class="alignnone size-medium wp-image-3597" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-powershell-commands.png" alt="appv-powershell-commands" width="300" height="184" /></a>

A new command "Enable-AppV" is added and based on the name we can assume this command can enable the App-V functionality within Windows. When running the command it shows an error.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/powershell-enable-appv.png" rel="attachment wp-att-3595"><img class="alignnone size-medium wp-image-3595" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/powershell-enable-appv.png" alt="powershell-enable-appv" width="300" height="61" /></a>

<strong>[Update]: </strong>It appears there is a difference between the x86 version of PowerShell and the x64 version. When running the command "Enable-AppV" in the x64 version it works. But as my fellow MVP <a href="http://stealthpuppy.com/enable-appv-uev-windows-10/" target="_blank">Aaron Parker</a> explained at this point there no feedback.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/powershell-enable-appv-good.png" rel="attachment wp-att-3606"><img class="alignnone size-medium wp-image-3606" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/powershell-enable-appv-good.png" alt="powershell-enable-appv-good" width="300" height="49" /></a>

As a alternative App-V can be enabled by enabling the App-V Client services. After this an App-V package can be added and should be able to successfully run it.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publish-package.png" rel="attachment wp-att-3594"><img class="alignnone size-medium wp-image-3594" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publish-package.png" alt="appv-publish-package" width="300" height="61" /></a> 

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-add-package.png" rel="attachment wp-att-3596"><img class="alignnone size-medium wp-image-3596" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-add-package.png" alt="appv-add-package" width="300" height="92" /></a> 

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-package-published.png" rel="attachment wp-att-3592"><img class="alignnone size-medium wp-image-3592" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-package-published.png" alt="appv-package-published" width="300" height="134" /></a> 

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-adobe-running.png" rel="attachment wp-att-3586"><img class="alignnone size-medium wp-image-3586" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-adobe-running.png" alt="appv-adobe-running" width="300" height="266" /></a>

<h2>Publishing server</h2>
The built-in App-V client should be compatible with an existing version 5.1 infrastructure but for some reason it could not sync with the publishing server.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publish-server.png" rel="attachment wp-att-3598"><img class="alignnone size-medium wp-image-3598" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publish-server.png" alt="appv-publish-server" width="300" height="153" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publishing-server-failed.png" rel="attachment wp-att-3593"><img class="alignnone size-medium wp-image-3593" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publishing-server-failed.png" alt="appv-publishing-server-failed" width="300" height="153" /></a>

At this point I have no idea what is causing this error. I will look into this and hopefully I can share results in an upcoming blog post.

<strong>[Update]: </strong>Also here using the correct version of PowerShell is important otherwise it will result in an error like above. When using the x64 version you can successfully sync with the publishing server.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publishing-server-good.png"><img class="alignnone wp-image-3605 size-medium" src="{{site.baseurl}}/assets/images/posts/2016-04-09-microsoft-app-v-built-in-windows-10/appv-publishing-server-good.png" alt="appv-publishing-server-good" width="300" height="168" /></a>

<h2>Conclusion</h2>
With the integration of App-V Microsoft is enabling all their enterprise customer to use their powerful application virtualization technology out of the box. This was a brief exploration into the App-V integration in Windows 10 but it shows it is not final yet. However I did manage to run a App-V package successfully. If you have comments or question please leave a comment.
<div id="spoon-plugin-kncgbdglledmjmpnikebkagnchfdehbm-2" style="display: none;"></div>
