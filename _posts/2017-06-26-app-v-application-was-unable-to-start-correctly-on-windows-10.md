---
layout: post
title: 'UPDATE: App-V application was unable to start correctly on Windows 10'
authors: [ryan]
comments: true
categories: [App-V]
tags: [1703, App-V, App-V, Creators Update, Error, Windows 10]
image: assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-application-was-unable-to-start-correctly-on-windows-10-feature-image.png
---
A couple weeks ago I received an email regarding an issue with App-V packages unable to start correctly on the latest version of Windows 10 (Creators Update, build 1703). When App-V packages are removed and added it won't start and produces an error. This blog post will take a closer look at this strange behavior on the latest release of Windows 10.

<!--more-->
<h2>Application was unable to start correctly</h2>
When an App-V package is removed and re-added on Windows 10 (Creators Update, build 1703) the following error will be shown during the startup of the App-V package.
<blockquote>The application was unable to start correctly (0xc0000142). Click OK to close the application.</blockquote>
<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-application-error.png"><img class="alignnone size-full wp-image-4338" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-application-error.png" alt="" width="476" height="165" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/windows-10-1703.png"><img class="alignnone size-medium wp-image-4336" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/windows-10-1703.png" alt="" width="300" height="234" /></a>

After the error, the App-V package is not able to start anymore and the only way to start the package again is to reboot the entire machine.
<h2>How to reproduce the error</h2>
Let me explain how to reproduce this error. In my case, I have a publishing server configured in my environment and once the user is logged on a couple App-V packages are added.

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-sync-publishing-server.png"><img class="alignnone size-medium wp-image-4334" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-sync-publishing-server.png" alt="" width="300" height="275" /></a>

When I start Adobe Reader the App-V package starts fine without any issues.

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-adobe-reader.png"><img class="alignnone size-medium wp-image-4337" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-adobe-reader.png" alt="" width="300" height="217" /></a>

Now let's remove and re-add all the packages.

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-remove-all-packages.png"><img class="alignnone size-medium wp-image-4333" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-remove-all-packages.png" alt="" width="300" height="275" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-sync-publishing-server.png"><img class="alignnone size-medium wp-image-4334" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-sync-publishing-server.png" alt="" width="300" height="275" /></a>

It will take a while before the application responds with the error message.

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-application-error.png"><img class="alignnone wp-image-4338 size-full" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-application-error.png" alt="" width="476" height="165" /></a>
<h2>Possible cause of the error</h2>
Within the Windows event log, the following error is logged.
<blockquote>{Registry Hive Recovered} Registry hive (file): '\??\C:\ProgramData\Microsoft\AppV\Client\VREG\Packages\fc6e9f2f-48d8-4ad9-92a2-7d50ec692c31\SIDS\S-1-5-21-1375947726-2994949748-3919233647-500\a178a63a-cfb1-4dfa-b5c3-7aa03c11967b\REGISTRY\MACHINE\SOFTWARE.dat' was corrupted and it has been recovered. Some data might have been lost.</blockquote>
<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-windows-event-log-error.png"><img class="alignnone size-medium wp-image-4335" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-windows-event-log-error.png" alt="" width="300" height="186" /></a>

According to the error, it seems the registry file of the App-V package is corrupt. To validate I have loaded the "Registry.dat" within the Registry Editor without any errors.

<a href="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-package-registry-dat.png"><img class="alignnone size-medium wp-image-4332" src="{{site.baseurl}}/assets/images/posts/2017-06-26-app-v-application-was-unable-to-start-correctly-on-windows-10/app-v-package-registry-dat.png" alt="" width="300" height="200" /></a>
<h2>Conclusion</h2>
This error may not occur in a default App-V scenario but it's possible when packages are regularly removed and added again. Currently, there is no real solution yet and the only way to avoid this nasty error is to reboot the machine. I hope Microsoft will follow up with an explanation and a fix. A very important takeaway: Always properly test the new releases of Windows 10 as they may break your environment!

Please let me know if you are encountering this same behavior or if you have any suggestions to further troubleshoot. Special thanks to Valery Atlas for reporting and helping troubleshoot this issue.

<h2>Update</h2>
It took some time but there is a patch! According to <a href="https://twitter.com/app2pack" target="_blank" rel="noopener">Vigneshwaran, </a>the following KB patches will fix the issue described above:

<a href="https://support.microsoft.com/en-in/help/4462939/windows-10-update-kb4462939" target="_blank" rel="noopener">October 18, 2018—KB4462939</a>

<a href="https://support.microsoft.com/en-in/help/4462932" target="_blank" rel="noopener">October 18, 2018—KB4462932</a>

<a href="https://support.microsoft.com/en-in/help/4462933/windows-10-update-kb4462933" target="_blank" rel="noopener">October 24, 2018—KB4462933</a>

Thanks for sharing!
