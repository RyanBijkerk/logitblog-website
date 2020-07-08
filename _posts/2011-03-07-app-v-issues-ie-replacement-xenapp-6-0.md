---
layout: post
title: App-V issues IE Replacement XenApp 6.0
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, Citrix, HTML, XenApp 6.0]
image: assets/images/posts/2011-03-07-app-v-issues-ie-replacement-xenapp-6-0/app-v-issues-ie-replacement-xenapp-60-feature-image.png
---
<p style="text-align: justify;">I had a problem with an App-V application within the XenApp web interface. The applications are streamed to a Windows server  2008 R2 and published in the web interface with XenApp 6.0. After publishing the App-V application in the web interface, the help function did not open correctly.</p>
<p style="text-align: justify;">The applications help function is a HTML file that is located in the bubble. When the help function is opened, Internet Explorer displayed the default homepage and not the help.html. Within the browser the help files cannot be found. Internet Explorer is running outside the App-V bubble and this is the reason that the HTML files cannot be found. The Internet Explorer replacement of XenApp is causing this problem. With a RDP session to the same server the application help function works correctly because the ICA client is not running in the user session.</p>
I searched for a solution and found the following article at Citrix.

<a href="http://support.citrix.com/article/CTX107424">http://support.citrix.com/article/CTX107424</a>
<p style="text-align: justify;">I followed the resolution steps but that didn't work. After trying and tweaking I added the following registry keys to the sequence.</p>

<blockquote><code>Windows Registry Editor Version 5.00
</code>
<code>[HKEY_LOCAL_MACHINE\Software\Classes\http\shell\open\command]
@="\"C:\\Program Files\\Internet Explorer\\iexplore.exe\" \"%1\""
</code>
<code>[HKEY_LOCAL_MACHINE\Software\Classes\https\shell\open\command]
@="\"C:\\Program Files\\Internet Explorer\\iexplore.exe\" \"%1\""
</code>
<code>[HKEY_LOCAL_MACHINE\Software\Classes\htmlfile\shell\opennew\command]
@="\"C:\\Program Files\\Internet Explorer\\iexplore.exe\" \"%1\""
</code>
<code>[HKEY_LOCAL_MACHINE\Software\Classes\htmlfile\shell\open\command]
@="\"C:\\Program Files\\Internet Explorer\\iexplore.exe\" \"%1\""</code></blockquote>
&nbsp;
<p style="text-align: justify;">After adding the registry keys the help function works correctly in the web interface and a published desktop.</p>
If you have questions or suggestions, leave a comment.
