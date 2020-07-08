---
layout: post
title: Custom shell extensions with App-V
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Notepad++, Regedit]
image: assets/images/posts/2011-02-08-custom-shell-extensions-with-app-v/custom-shell-extensions-with-app-v-feature-image.png
---
<p style="text-align: justify;">Microsoft App-V doesn't support custom shell extensions because this would require providing Windows Explorer access to the virtual environment. Some applications requires this shell extension for the functionality of the application. <a title="More information about Notepad++" href="http://notepad-plus-plus.org/" target="_blank">Notepad++</a> is one of the applications that has a custom shell extension to quickly edit a script or text file. When you sequence Notepad++ the custom shell extension is not captured. This can be added on the machine where the application is deployed.</p>
<p style="text-align: justify;">In the registry you have to create a key under the following hive:
LOCAL_MACHINE\Software\Classes\*\Shell\ or CURRENT_USER\Software\Classes\*\Shell\
Create the key with the name of the application. In the default string value add the custom text like "Logit Blog is awesome" or just "Edit with Notepad++". To add an icon create a new string value named "Icon" and add in the value data the location of the icon.
To launch the application create a new key named command and add SFTTRAY /Launch "&lt;Appname&gt;" %1 to the default string value.</p>

<blockquote style="text-align: justify;">Windows Registry Editor Version 5.00</blockquote>
<blockquote>[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\*\shell\Notepad]
@="Edit with Notepad++"
"icon"="D:\\Apps\\VApps\\NOTEPAD.585\\NOTEPAD.585 Icons\\Notepad++.ico"</blockquote>
<blockquote>[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\*\shell\Notepad\command]
@="sfttray /launch \"Notepad++\" "%1""</blockquote>
<p style="text-align: justify;"><img class="size-full wp-image-386 alignright" src="{{site.baseurl}}/assets/images/posts/2011-02-08-custom-shell-extensions-with-app-v/Context.png" alt="" width="337" height="339" />This way it's possible to create custom shell extensions for  App-V applications. You can launch the application with the correct parameter if nescessary, just add the parameters after %1. The %1 is passed as the path to the file.</p>
<p style="text-align: justify;">It's also possible to import the setting under the CURRENT_USER hive.</p>
<p style="text-align: justify;">It's important to remember that this is not supported by Microsoft. Also the custom shell extension will remain on the machine when the App-V application is not available. This can generate an error message when you use the custom shell extension.</p>
When you have any suggestions please leave a comment.
