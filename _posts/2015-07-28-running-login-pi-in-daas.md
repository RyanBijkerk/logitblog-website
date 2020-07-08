---
layout: post
title: Running Login PI in DaaS
authors: [ryan]
comments: true
categories: [Login VSI]
tags: [Amazon Workspaces, DaaS, Login PI, Login PI, User Experience]
image: assets/images/posts/2015-07-28-running-login-pi-in-daas/running-login-pi-in-daas-feature-image.png
---
When having a desktop from a DaaS provider you as IT administrators are not in control over the hosting. But how do you get the extra insights in your DaaS desktop? Login PI can give the extra insights of the end-user experience in the DaaS environment. This blog post is all about running Login PI in DaaS.
<blockquote><strong>Note</strong>: This is not a official Login VSI blog post and this scenario is not officially supported.</blockquote>
<h2>What is Login PI?</h2>
Login PI helps enterprises improve availability and maintain a consistent, high-quality end user experience by providing valuable information about the performance of their centralized desktop environments. Using virtual users with real-world workloads, IT administrators can identify performance problems before they impact end users. Login PI integrates with existing management consoles and workloads can be tailored to meet enterprise application requirements.
<h2>Database configuration</h2>
One of the dependencies of Login PI is the connection towards the database. When running Login PI in a multi domain scenario integrated security (active directory authentication) cannot be used. Besides that, the SQL server needs to be TCP accessible from the DaaS desktop. For this scenario I used a hosted Microsoft SQL database running in Azure.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/Azure-Database-Overview.png"><img class="alignnone size-medium wp-image-3324" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/Azure-Database-Overview.png" alt="Azure database overview" width="300" height="199" /></a>

By default all traffic is blocked to the Azure database and therefore the IP addresses that are allowed to access needs to be added.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/Azure-Database-Firewall.png"><img class="alignnone size-medium wp-image-3323" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/Azure-Database-Firewall.png" alt="Azure database firewall" width="300" height="162" /></a>

During the Login PI installation the database details can be entered and the database initialization will take place.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Database-Config.png"><img class="alignnone size-medium wp-image-3320" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Database-Config.png" alt="Login PI database config" width="300" height="225" /></a>
<h2>DaaS desktop</h2>
In this scenario I used a Amazon Workspaces Desktop. Within the desktop I installed Microsoft Office 2013 with my own license. This way I can use the default "Office apps" workloads which contains Microsoft Office application and Adobe reader.

By default Login PI will copy the engine component to the desktop from a file share, but this is not possible in a DaaS desktop. In this case you can create a file share on the DaaS desktop it self which will contain all the required files.

On your Login PI server zip the shared directory and copy this towards the DaaS desktop. Share the directory so it is accessible by using "\\LOCALHOST\". This can also be a dedicated file server somewhere in the environment, but it should be accessible by the (virtual) user and from the desktop.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Amazon-Share.png"><img class="alignnone size-medium wp-image-3316" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Amazon-Share.png" alt="Login PI Amazon share" width="300" height="293" /></a>

For the user you want to use as the Login PI virtual user make a shortcut in profile start up directory towards: \\LOCALHOST\shared\bin\Target\Login.cmd". This way when the user is logged on the engine will be started.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Amazon-Logon.png"><img class="alignnone size-medium wp-image-3315" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Amazon-Logon.png" alt="Login PI Amazon logon" width="300" height="290" /></a>
<h2></h2>
<h2>Login PI configuration</h2>
By default Login PI delivers content that can be used within a workload. By default the share where Login PI is installed is added which needs to be changed to LOCALHOST or the shared location in the DaaS environment. This needs to be done for the Dataservers and Webservers.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-DataWeb-Server.png"><img class="alignnone size-medium wp-image-3321" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-DataWeb-Server.png" alt="Login PI Data/Web -server" width="300" height="167" /></a>

After this you complete configuring the profile. Please note by default there is no connector for Amazon Workspaces available. In this case you can use the connector Direct Desktop and replace it by a PowerShell script. This can be done within the Advanced Settings section on the Connection page.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Connector.png"><img class="alignnone size-medium wp-image-3317" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Connector.png" alt="Login PI custom connector" width="300" height="167" /></a>

In an upcoming blog post I will share the script that I used for connecting towards Amazon Workspaces.
<h2>Results</h2>
If everything is configured correctly data will be collected and results are going to be visible. These are some results that I have collected from the Amazon Workspaces desktop.

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Data-average.png"><img class="alignnone size-medium wp-image-3319" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Data-average.png" alt="LoginPI data average" width="300" height="93" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Data.png"><img class="alignnone size-medium wp-image-3318" src="{{site.baseurl}}/assets/images/posts/2015-07-28-running-login-pi-in-daas/LoginPI-Data.png" alt="Login PI data" width="300" height="189" /></a>
<h2>Conclusion</h2>
Login PI in DaaS really can give some extra information from a end-user perspective. Besides the dependencies towards the database and the share you still can use the Login PI in a DaaS scenario. This can provide interesting data and insight in to the DaaS desktop.
