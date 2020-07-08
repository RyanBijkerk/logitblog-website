---
layout: post
title: App-V Mozilla Firefox set Desktop Background issue
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Desktop Background, Mozilla Firefox, Powershell]
image: assets/images/posts/2012-05-22-app-v-firefox-set-desktop-background/app-v-mozilla-firefox-set-desktop-background-issue-feature-image.png
---
On my corporate laptop I use a virtual Mozilla Firefox sequenced with Microsoft App-V. I created the package with all the plugins and settings that I prefer. One day I discovered strange behavior when I applied a desktop background from the virtual package.

<img class="alignright size-full wp-image-1527" title="" src="{{site.baseurl}}/assets/images/posts/2012-05-22-app-v-firefox-set-desktop-background/firefox-set-wallpaper.png" alt="" width="220" height="201" />I applied the background with the quick option in Firefox, right click on the image and then Set As Desktop Background...

After I applied the wallpaper I was not satisfied about the alignment of the background so I opened the personalize option from my desktop to change the settings.

In the personalization screen the Desktop Background is missing while the background is applied.

<img class="alignnone size-full wp-image-1529" title="" src="{{site.baseurl}}/assets/images/posts/2012-05-22-app-v-firefox-set-desktop-background/firefox-desktop-personlize.png" alt="" width="621" height="111" />

Because I created my App-V package with predefined settings Firefox has created a profile in "%Appdata%\Mozilla\" inside the App-V bubble. When a background is applied Firefox will save a copy of the picture in "%Appdata%\Mozilla\Firefox\Desktop Background.bmp" and applies the location of the background in "HKCU\Control Panel\Desktop" String:Wallpaper. When I log off or reboot my machine Windows cannot access the background file and will display a solid color.

But how do you prevent this behavior when you deploy a Mozilla Firefox package in your environment?

It is possible to exclude the "%Appdata%\Mozilla" directory from your package. When Firefox is started new application data will be created outside the bubble. But it is not possible to set predefined settings unless you use a profile management tool like RES, Immidio Flex Profiles or AppSense.

It is also possible to create a shutdown script in the App-V package that will copy the desktop background outside the bubble to %Appdata% and apply the new location in the physical registry. Therefore I created two Powershell scripts.

The following script is to copy the desktop background outside the bubble.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$Appdata = $env:Appdata
$Background = "$Appdata\Mozilla\Firefox\Desktop Background.bmp"

Copy-Item $Background "$Appdata" -Recurse</pre>
The following script is to set the new location in the physical registry. It will only apply if the desktop background is set by Mozilla Firefox.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$Appdata = $env:Appdata
$Background = "$Appdata\Mozilla\Firefox\Desktop Background.bmp"

$Current = Get-ItemProperty -Path "HKCU:Control Panel\Desktop"
If ($Current.Wallpaper -like $Background) {
Set-Itemproperty -Path "HKCU:Control Panel\Desktop" -Name WallPaper -Value "$Appdata\Desktop Background.bmp"
}</pre>
The get the scripts working you need to add the following lines to the Mozilla Firefox OSD. Make sure the correct script is running inside/outside the App-V bubble.
<pre class="lang:xhtml decode:true brush: html; gutter: false ">&lt;SCRIPT EVENT="SHUTDOWN" TIMING="POST" PROTECT="TRUE" WAIT="TRUE" TIMEOUT=""&gt;
	&lt;SCRIPTBODY&gt;Powershell.exe -file "D:\Data\Scripts\Copy-Background.ps1"&lt;/SCRIPTBODY&gt;
&lt;/SCRIPT&gt;
&lt;SCRIPT EVENT="SHUTDOWN" TIMING="POST" PROTECT="FALSE" WAIT="TRUE" TIMEOUT=""&gt;
	&lt;SCRIPTBODY&gt;Powershell.exe -file "D:\Data\Scripts\Set-Background.ps1"&lt;/SCRIPTBODY&gt;
&lt;/SCRIPT&gt;</pre>
Note: Don't forget to change the location of the script depending on your environment and make sure the Execution Policy in Powershell is not on restricted.

This way the desktop background will be available after a reboot or log off. If you have an other suggestion how to resolve this please leave a comment.
