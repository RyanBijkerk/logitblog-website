---
layout: post
title: VMware UEM application blocking
authors: [ryan]
comments: true
categories: [VMware]
tags: [Security, UEM, VMware, VMware]
image: assets/images/posts/2016-04-03-vmware-uem-application-blocking/vmware-uem-application-blocking-feature-image.png
---
Security attacks in a managed desktop environment is always a threat. It is common within larger organizations to have a locked-down desktop environment to prevent accidental execution of malicious programs. On the 17th of March VMware released the new version of VMware User Environment Management (UEM). This blog post will take a closer look at the new UEM application blocking functionality.
<h2>Enabling UEM application blocking:</h2>
The new application blocking can be found in the the User Environment tab. <a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block.png" rel="attachment wp-att-3567"><img class="alignnone size-medium wp-image-3567" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block.png" alt="uem-app-block" width="300" height="197" /></a>

By default the application blocking is not enabled. In order to enable the functionality open the global configuration and check the checkbox.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-enable.png" rel="attachment wp-att-3562"><img class="alignnone size-medium wp-image-3562" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-enable.png" alt="uem-app-block-enable" width="300" height="237" /></a>
<h2>Block and allow applications:</h2>
When creating a new application block setting there is the ability to allow and block applications. Here you can specify specific files or entire directories. When configuring a directory any program started from the location is blocked.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-setting.png" rel="attachment wp-att-3563"><img class="alignnone size-medium wp-image-3563" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-setting.png" alt="uem-app-block-setting" width="223" height="300" /></a>

<strong>Please note:</strong> when blocking all application in the "C:\Windows\System32\" directory the user cannot log on. Like shown in the screenshot multiple programs are required.
<h2>Saved format and location:</h2>
Just like any other setting in UEM a xml file is created within the share.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-dir.png" rel="attachment wp-att-3560"><img class="alignnone size-medium wp-image-3560" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-dir.png" alt="uem-app-block-dir" width="300" height="221" /></a>

In the xml the setting is specified by "applicationBlocking". Blocked applications are specified by "block" and allowed applications are specified by "allow" entry.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-xml.png" rel="attachment wp-att-3564"><img class="alignnone size-medium wp-image-3564" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-block-xml.png" alt="uem-app-block-xml" width="300" height="207" /></a>
<h2>Blocked application:</h2>
When an user is starting a blocked application he or she is informed by the following message.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-blocked.png" rel="attachment wp-att-3561"><img class="alignnone size-medium wp-image-3561" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-app-blocked.png" alt="uem-app-blocked" width="300" height="73" /></a>
<h2>Log blocked application:</h2>
From a security point of view it is interesting to monitor the attempts to start a blocked application. Within the group policy objects of UEM logging can be enabled. When an attempt is done an entry will be created within the event log.

<a href="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-gpo-logging.png" rel="attachment wp-att-3565"><img class="alignnone size-medium wp-image-3565" src="{{site.baseurl}}/assets/images/posts/2016-04-03-vmware-uem-application-blocking/uem-gpo-logging.png" alt="uem-gpo-logging" width="300" height="276" /></a>

In my example I have blocked all the processes within the "C:\Windows\System32\" directory. In that case it is useful to log attempts to start a blocked application. This helped me to understand which process are needed for an user logon.

<strong>Conclusion:</strong>

With this security feature, VMware UEM is becoming even more interesting for larger organisations. Security is nowadays very important within desktop environment by the all day threat of malicious programs. I hope this blog post is helpful and if you have question please leave a comment.
<div id="spoon-plugin-kncgbdglledmjmpnikebkagnchfdehbm-2" style="display: none;"></div>
