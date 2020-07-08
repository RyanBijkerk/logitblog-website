---
layout: post
title: App-V Application Service Issue
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Applications, SC, SC.exe, Services, Services Error 5]
image: assets/images/posts/2012-04-12-app-v-application-service-issue/app-v-application-service-issue-feature-image.png
---
During sequencing of an application I received an error about the services (App-V Sequencer 4.6). The installer of the application installs a service that is required for the application. Normally the installer starts the service and continues the installation but this time the following error message occurred.

<img class="size-full wp-image-1478 alignnone" src="{{site.baseurl}}/assets/images/posts/2012-04-12-app-v-application-service-issue/Screenshot_Error_Installer.png" alt="" width="416" height="145" />
The installer cannot start the service and the installations failed.
The retry button tries to start the service again but the error returns. In the services.msc it is not possible to start the service or check the properties of the application services.
When opening the properties I received the following error.

<img class="alignnone size-full wp-image-1479" title="" src="{{site.baseurl}}/assets/images/posts/2012-04-12-app-v-application-service-issue/Screenshot_Services_Properties.png" alt="" width="228" height="145" />

When I try to start the service I received the following error.

<img class="alignnone size-full wp-image-1480" title="" src="{{site.baseurl}}/assets/images/posts/2012-04-12-app-v-application-service-issue/Screenshot_Services_Start.png" alt="" width="402" height="165" />

I exported the service settings from the registry under the following key.
<pre class="brush: text; gutter: false">"HKLM\System\CurrentControlSet\services\&lt;ServicesName&gt;".</pre>
With the information I can re-create the services manually.
I removed the registry key and removed the service with SC.exe using the following command.
<pre class="brush: bash; gutter: false">SC.exe delete &lt;ServicesName&gt;</pre>
<span style="text-decoration: underline;"><strong>Note:</strong></span> Make sure the Services.msc is closed.

With the information from the export it is possible to re-create the services. I used the following command and parameters.
<pre class="brush: bash; gutter: false">SC.exe create &lt;ServicesName&gt; binPath= "C:\Program Files\Common Files\&lt;Application Dir&gt;\&lt;Application Executable&gt;" DisplayName= "&lt;Application DisplayName&gt;"</pre>
<span style="text-decoration: underline;"><strong>Note:</strong></span> The parameters are depending on the requirement of the services.

To start the services you can open the Services.msc or use the following command.
<pre>SC.exe start &lt;ServicesName&gt;</pre>
After these steps it was possible to continue the installation finish the App-V package.
If you have any comments or questions, please leave a comment.
