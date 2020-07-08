---
layout: post
title: App-V 5 SMB Publishing Time OS compare
date: 2014-10-05 19:07
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, GAP-IT, Powershell, Windows 7, Windows 8.1 Windows 10]
image: assets/images/posts/2014-10-05-app-v-5-smb-publishing-time-os-compare/app-v-5-smb-publishing-time-os-compare-feature-image.png
---
When migrating to a newer operating system will the App-V publishing time be different? When publishing times are important you don't want to be surprised with increasing times. With the release of Windows 10 Technical Preview I was wondering if there is a difference in publishing times with Windows 7, Windows 8.1 and Windows 10. In this blog post I will share the result I collected in this research.
<h2>Testing methodology</h2>
This is tested within a small virtual environment running with Hyper-V on my laptop. The environment has the following VM's:
<ul>
 	<li>AppV-Infra-01 - Domain Controller - Windows Server 2012</li>
 	<li>AppV-Server-01 - App-V Management / Publishing Server - Windows Server 2012 R2</li>
 	<li>AppV-Client-01 - App-V Client - Windows 7 Enterprise</li>
 	<li>AppV-Client-02 - App-V Client - Windows 8.1 Enterprise</li>
 	<li>AppV-Client-03 - App-V Client - Windows 10 Technical Preview</li>
</ul>
All three App-V client machines are running a x86 operating system and configured with 1 GB start-up memory with dynamical expanding memory enabled and 4vCPU's.

App-V 5.0 SP2 Hotfix 5 is installed on each client VM and Windows Defender is disabled for performance reasons. No further optimization are applied.

On each client VM Autologon is configured with a domain user which has 8 App-V packages assigned to be published. All App-V packages are fault sequenced and streaming over SMB. The packages does not contain VC runtimes which can affect the publishing performance. For more information please read the following <a href="http://www.tmurgent.com/AppV/images/WhitePapers/Research_VCRuntimes.pdf" target="_blank">whitepaper</a>.

After the user successfully logged on and the packages are published the times are collected with the tool GAP-IT which is running on AppV-Server-01. When GAP-IT is finished the following powershell script is used to remove all packages and reboots the VM.
<pre class="lang:ps decode:true">Write-Host "Removing all App-V Packages..."

$appvPackages = Get-AppvClientPackage -All

Net stop AppVClient

$appvPackages | Remove-AppVClientPackage

Net Start AppVClient

Write-Host "Done going to reboot the machine..."

Start-Process -FilePath "Shutdown.exe" -ArgumentList "-r -t 00"</pre>
Each operating system is individually tested which means only the tested VM is running during collecting all the publishing times.

The testing cycle is repeated 5 times and the average times are used in the results.
<h2>Results</h2>
The following results show Windows 8.1 and Windows 10 are a bit faster compared to Windows 7.

<a href="{{site.baseurl}}/assets/images/posts/2014-10-05-app-v-5-smb-publishing-time-os-compare/SMB-PubTime.png"><img class="alignnone size-large wp-image-2979" src="{{site.baseurl}}/assets/images/posts/2014-10-05-app-v-5-smb-publishing-time-os-compare/SMB-PubTime.png" alt="SMB-PubTime" width="584" height="323" /></a>

The following chart will show the overall publishing time in a percentage bar chart. Here we can see Windows 8.1 is 4% faster and Windows 10 is 9% faster compared to Windows 7.

<a href="{{site.baseurl}}/assets/images/posts/2014-10-05-app-v-5-smb-publishing-time-os-compare/SMB-PubPrecent.png"><img class="alignnone size-large wp-image-2978" src="{{site.baseurl}}/assets/images/posts/2014-10-05-app-v-5-smb-publishing-time-os-compare/SMB-PubPrecent.png" alt="SMB-PubPrecent" width="584" height="257" /></a>
<h2>Conclusion</h2>
This research shows a newer operating system will result in a better publishing time when streaming over SMB. This can be related to the SMB protocol which is on Windows 7 SMB 2 and Windows 8.1 and higher SMB 3. So upgrading to a newer operating system will result is a slightly faster publishing time.

In an upcoming blog post I will share the results while streaming over HTTP.

Want to collect App-V publishing times in your environment? Check out GAP-IT <a href="http://www.logitblog.com/tooling/gap-it/" target="_blank">here</a>.

If you have a comment or question please leave them below.
