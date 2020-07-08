---
layout: post
title: App-V automatically cleanup unpublished packages
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, cleanup, Microsoft, publishing]
image: assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-automatically-cleanup-unpublished-packages-feature-image.png
---
With the release of Microsoft's Creators Update (1703) new features have been added to App-V. These new features are summarized on this<a href="https://technet.microsoft.com/en-us/itpro/windows/manage/appv-about-appv" target="_blank" rel="noopener noreferrer"> page</a>. One of the new functionalities is the automatically cleanup unpublished packages and in this blog post, I will cover the new cleanup functionality.
<h2>What is the advantage of auto cleanup?</h2>
In previous versions of App-V, once an App-V package is unpublished the content still exists on the disk. To free up additional space you need to manually remove the unpublished package. When managing a large environment this can result can be a hassle. With the new update of Windows 10 (build 1703) App-V automatically cleanup unpublished packages. Once the package is unpublished the content will be removed from the disk. This way you free up additional storage and no packages are left behind.
<h2>How do you enable auto cleanup?</h2>
There are multiple ways to configure the auto cleanup.
<ul>
 	<li>PowerShell</li>
 	<li>Group policy</li>
 	<li>Registry</li>
</ul>
Within PowerShell, the following command can be used to enable or disable the feature.
<strong>Enable:</strong>
<pre class="lang:ps decode:true"> Set-AppvClientConfiguration -AutoCleanupEnabled 1</pre>
<strong>Disable:</strong>
<pre class="lang:ps decode:true"> Set-AppvClientConfiguration -AutoCleanupEnabled 0</pre>
You can verify the configuration using the command below.
<pre class="lang:ps decode:true"> Get-AppvClientConfiguration</pre>
<a href="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/get-appvclientconfiguration.png"><img class="alignnone size-medium wp-image-4259" src="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/get-appvclientconfiguration.png" alt="" width="300" height="205" /></a>

When managing a larger environment with multiple clients you may want to switch to Group Policy. You can find the cleanup feature in the following location.

"Administrative Templates\System\App-V\PackageManagement\Enable automatic cleanup of unused appv packages setting."

<strong>Note</strong>: for some reason, this feature is not yet included in the <a href="https://www.microsoft.com/en-us/download/details.aspx?id=54957" target="_blank" rel="noopener noreferrer">Microsoft Desktop Optimization Pack Group Policy Administrative Template</a>s<a href="https://www.microsoft.com/en-us/download/details.aspx?id=54957" target="_blank" rel="noopener noreferrer">.</a>

Finally, there is also the possibility to enable or disable it by the registry.

"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\PackageManagement\AutoCleanupEnabled"

<a href="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-registry.png"><img class="alignnone size-medium wp-image-4258" src="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-registry.png" alt="" width="300" height="206" /></a>
<h2>When are the packages removed?</h2>
Once the package is unpublished the App-V package will be removed. The following screenshot is taken before the package is unpublished.

<a href="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-full.png"><img class="alignnone size-medium wp-image-4261" src="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-full.png" alt="" width="300" height="158" /></a>

After unpublishing the package the directory is empty.

<a href="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-empty.png"><img class="alignnone size-medium wp-image-4260" src="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-empty.png" alt="" width="300" height="158" /></a>

As you can see the directory still exists in the App-V folder but the version has been removed. It's important to know when a package is still published to another user on the same client the package will not be removed. This makes sense otherwise the other user cannot start the App-V application anymore.

<a href="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-still-published.png"><img class="alignnone size-medium wp-image-4267" src="{{site.baseurl}}/assets/images/posts/2017-04-20-app-v-automatically-cleanup-unpublished-packages/appv-package-still-published.png" alt="" width="300" height="146" /></a>
<h2>Conclusion</h2>
Microsoft is adding new functionalities to App-V using the major builds of Windows. With the Creators Update, multiple features are added including the auto cleanup. This auto cleanup may be a small feature it can really help free up storage when unpublishing packages from a client. I hope this was useful and if you have any comments or question please leave them below.

<span style="text-decoration: underline;">Credit featured image:</span> <a href="https://unsplash.com/search/wash?photo=hQOHDAibf6A" target="_blank" rel="noopener noreferrer">Catt Liu</a>
