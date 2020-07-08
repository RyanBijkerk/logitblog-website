---
layout: post
title: App-V 4.x & 5.x integration with Login VSI
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Login VSI, Workloads]
image: assets/images/posts/2014-01-09-app-v-4-x-5-x-integration-with-login-vsi/app-v-4x-5x-integration-with-login-vsi-feature-image.png
---
Login VSI supports application virtualization, but how do you integrate App-V within a workload? Depending which applications are virtualized it is possible to run the medium workload without any modifications. When every application is virtualized some modifications are required. Because Login VSI v4 has a simple workload language, App-V can be easily integrated. In this blog post I will explain the App-V 4.x &amp; 5.x integration with Login VSI.
<blockquote><strong style="font-style: italic;">Please note:</strong><span style="font-family: Georgia, 'Bitstream Charter', serif; font-style: italic;"> Workload customization/modification requires a Login VSI PRO license. More information can be found <a href="http://www.loginvsi.com" target="_blank">here</a>.</span></blockquote>
<h2>General configuration</h2>
Because Login VSI uses file type association it is easy to integrate App-V in a workload. To run the medium workload without any modifications Microsoft Outlook, Microsoft Word and Adobe Flash cannot be virtualized. When all application are virtualized the workload needs to be modified. In this case we will create a workload where every application is virtualized.
<blockquote><strong>Please note:</strong> With App-V 4.x the basephase cannot be used because Microsoft Word is started directly during the basephase.</blockquote>
First we need to do some configurations in the Login VSI management console. To prevent the Freemind file type association be overwritten we need to configure to not set the file type association during the preparation. Open the Login VSI management console and go to the workload settings section.

<a href="{{site.baseurl}}/assets/images/posts/2014-01-09-app-v-4-x-5-x-integration-with-login-vsi/AppV-set-Freemind-fta.png"><img class="alignnone wp-image-2704" src="{{site.baseurl}}/assets/images/posts/2014-01-09-app-v-4-x-5-x-integration-with-login-vsi/AppV-set-Freemind-fta.png" alt="" width="300" height="189" /></a>

Uncheck "Set freemind FTA" setting.

I recommend to set Log application start time because this can provide interesting information. When this option is enabled Login VSI will collect the application start time of each application. This can help to get a better understanding about the virtualized applications.

<a href="{{site.baseurl}}/assets/images/posts/2014-01-09-app-v-4-x-5-x-integration-with-login-vsi/AppV-appstart-time.png"><img class="alignnone wp-image-2703" src="{{site.baseurl}}/assets/images/posts/2014-01-09-app-v-4-x-5-x-integration-with-login-vsi/AppV-appstart-time.png" alt="" width="300" height="197" /></a>

Check "Log application start times" setting.
<h2>App-V 4.x workload</h2>
For App-V 4.x we can use the SFTTRAY.exe to start the applications. Within the workload we use the App_Start command to start the virtual package. Different segments needs to be modified in the workload. We will start in the prepare segment.

First we need to make sure Microsoft Outlook has a profile configured. Because this is within a virtual package we need to inject the registry setting within the virtual bubble. This can be done by the following command:
<pre class="brush: text; gutter: false">SFTTRAY.exe /exe reg.exe /Launch "Microsoft Outlook 2010" add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Outlook\Setup /v importPRF /t REG_SZ /d %TMP%\VSI\Runtime\Outlook.prf</pre>
Replace the ImportPRF VSI_RegWrite with the following workload command:
<pre class="brush: text; gutter: false">VSI_ShellExecute("Workload", "SFTTRAY.exe",  "/exe reg.exe /Launch "Microsoft Outlook 2010" add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Outlook\Setup /v importPRF /t REG_SZ /d %TMP%\VSI\Runtime\Outlook.prf")</pre>
For Microsoft Powerpoint the hardware acceleration is disabled. If this is not set within the virtual package this can be done within the prepare segment. The clean command is as followed:
<pre class="brush: text; gutter: false">SFTTRAY.exe /exe reg.exe /Launch "Microsoft PowerPoint 2010" add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Powerpoint\Option /v DisableHardwareNotification /t REG_DWORD /d 1</pre>
Replace the VSI_RegWrite with the following workload command:
<pre class="brush: text; gutter: false">VSI_ShellExecute("Workload", "SFTTRAY.exe",  "/exe reg.exe /Launch "Microsoft PowerPoint 2010" add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Powerpoint\Option /v DisableHardwareNotification /t REG_DWORD /d 1")</pre>
During the prepare phase Microsoft Word will be started and closed. To make sure the virtual package is used we need to replace the App_Start command with the following workload command:
<pre class="brush: text; gutter: false">App_Start("WordPrepare", "C:\Program Files\Microsoft Application Virtualization Client", "SFTTRAY.exe", "Title", "Word", "", "/Launch "Microsoft Word 2010"")</pre>
Within the prepare segment there are some registry tweaks to let the workload run smoothly. In my case it was not required to include all registry tweaks within the virtual packages. If you experience issues within some Office applications you probably need to modify the following two sections:

"Prevent excel book is windowed" &amp; "Deleting Office auto-repair documents"

Now that we have finished the prepare segment we can edit the other segments. During the workload there are two applications that are <span style="text-decoration: underline;">not</span> started by the file type association, Microsoft Outlook and Internet Explorer including Adobe Flash. We need to make sure these applications are started by the SFTTRAY.

Within segment 1 we need to start Microsoft Outlook. Replace the current App_Start with the following workload command:
<pre class="brush: text; gutter: false">App_Start("Outlook", "C:\Program Files\Microsoft Application Virtualization Client", "SFTTRAY.exe", "Title", "lang:Outlook:lang", "", "/Launch "Microsoft Outlook 2010"")</pre>
Within segment 4 we need to start Internet Explorer with Adobe Flash. Replace the current Web_Start with the following workload command:
<pre class="brush: text; gutter: false">App_Start("IE5", "C:\Program Files\Microsoft Application Virtualization Client", "SFTTRAY.exe", "Title", "Login VSI Flash Game", "", "/Launch "Internet Explorer" %VSI_WebLocation%\FlashGame\index.html")</pre>
Save the workload file and the App-V workload can be used including the virtual applications.
<h2>App-V 5.x workload</h2>
For App-V 5.x we can directly start the applications. Within the workload we use the App_Start command to start the virtual package. Different segments need to be modified in the workload. We will start in the prepare segment.

First we need to make sure Microsoft Outlook has a profile configured. Because this is within a virtual package we need to inject the registry setting within the virtual bubble. This can be done by the following command:
<pre class="brush: text; gutter: false">reg.exe /AppVVE:AE2439BA-A9CF-4220-A7F0-BBB378A29EDD_04244DE7-A338-4944-8CF0-2013926DCB59 add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Outlook\Setup /v importPRF /t REG_SZ /d %TMP%\VSI\Runtime\Outlook.prf</pre>
Please note the packageGUID and versionGUID are unique for each package. To get the packageGUID and versionGUID run the following command in powershell:
<pre class="brush: powershell; gutter: false">Get-AppvClientPackage</pre>
More information can be found <a href="http://support.microsoft.com/kb/2848278" target="_blank">here</a>.

To make sure the workload can execute this, replace the action with the following workload command:
<pre class="brush: text; gutter: false">VSI_ShellExecute("Workload", "reg.exe",  "/AppVVE:AE2439BA-A9CF-4220-A7F0-BBB378A29EDD_04244DE7-A338-4944-8CF0-2013926DCB59 add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Outlook\Setup /v importPRF /t REG_SZ /d %TMP%\VSI\Runtime\Outlook.prf")</pre>
For Microsoft Powerpoint the hardware acceleration is disabled. If this is not set within the virtual package this can be done within the prepare segment. The clean command is as followed:
<pre class="brush: text; gutter: false">reg.exe /AppVVE:AE2439BA-A9CF-4220-A7F0-BBB378A29EDD_04244DE7-A338-4944-8CF0-2013926DCB59 add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Powerpoint\Options /v DisableHardwareNotification /t REG_DWORD /d 1</pre>
Replace the VSI_RegWrite with the following workload command:
<pre class="brush: text; gutter: false">VSI_ShellExecute("Workload", "reg.exe",  "/AppVVE:AE2439BA-A9CF-4220-A7F0-BBB378A29EDD_04244DE7-A338-4944-8CF0-2013926DCB59 add HKEY_CURRENT_USER\Software\Microsoft\Office\%VSI_Office_Version%.0\Powerpoint\Options /v DisableHardwareNotification /t REG_DWORD /d 1")</pre>
Within the prepare segment there are some registry tweaks to let the workload run smoothly. In my case it was not required to include all registry tweaks within the virtual packages. If you experience issues within some Office applications you probably need to modified the following two sections:

"Prevent excel book is windowed" &amp; "Deleting Office auto-repair documents"

Now that we have finished the prepare segment we can edit the workload segments. During the workload there are two applications that are not started by the file type association, Microsoft Outlook and Internet Explorer including Adobe Flash. We need to make sure these applications are started within the package root directory.

Within segment 1 we need to start Microsoft Outlook. Replace the current App_Start with the following workload command:
<pre class="brush: text; gutter: false">App_Start("Outlook", "%ALLUSERSPROFILE%\Microsoft\AppV\Client\Integration\7AFDB884-2ED9-44A1-A69F-0D6206945C55\Root\Office14\", "Outlook.exe", "Title", "lang:Outlook:lang")</pre>
Within segment 4 we need to start Internet Explorer with Adobe Flash. Replace the current Web_Start with the following workload command:
<pre class="brush: text; gutter: false">App_Start("IE5", "C:\Program Files\Internet Explorer\", "iexplore.exe", "Title", "Login VSI", "", "/appvve:9DD1E58B-5FED-4EA7-A9C3-7520D1E76989_E9D056E8-7304-42E8-97B6-E52683B8156B %VSI_WebLocation%\FlashGame\index.html")</pre>
Save the workload file and the App-V workload can be used including the virtual applications.
<h2>Conclusion</h2>
With Login VSI it is simple to integrate Microsoft App-V or any other virtualization technique within a workload. When Microsoft Outlook, Microsoft Word and Adobe Flash are not virtualized you can even run the official medium workload. You just have to make sure the file type association for the virtual applications are set correctly.

The complete example workload file can be found here: <a href="http://www.logitblog.com/downloads/appv-4-medium.txt" target="_blank">App-V 4.x</a> &amp; <a href="http://www.logitblog.com/downloads/appv-5-medium.txt" target="_blank">App-V 5.x.</a>

If you have comments or questions just leave a comment.
