---
layout: post
title: 'Hands-on: Turbo Containers'
authors: [ryan]
comments: true
categories: [Containers]
tags: [Containers, Turbo, Virtualization]
image: assets/images/posts/2016-03-21-hands-on-turbo-containers/hands-on-turbo-containers-feature-image.png
---
The application container technology promises to change the way that IT operations are carried out just as virtualization technology did a few years previously. Thanks to my fellow MVP <a href="https://twitter.com/Rorymon" target="_blank">Rory Monaghan</a> I had the opportunity to try out Turbo which is a software virtualization and container technology. So I decided that have a go with Turbo and in this blog post I will share my hands-on experience.
<h2>What is Turbo and how does it work:</h2>
Turbo.net is a leading developer of software virtualization and container technology. Available as both a web-based service and on-site server, Turbo.net allows software publishers, enterprises, and schools to build, test, deploy, and manage desktop application containers instantly to any user with an Internet connection.

Turbo containers are built on top of the <strong>Spoon Virtual Machine Engine</strong> (SVM), an application virtualization engine which provides lightweight implementation of core operating system APIs, including the filesystem, registry, process, networking, and threading subsystems. Applications executing within the Turbo virtual machine interact with a virtualized filesystem, registry, network, and process environment supplied by the SVM, rather than directly with the host device operating system.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-overview.png" rel="attachment wp-att-3541"><img class="alignnone size-medium wp-image-3541" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-overview.png" alt="turbo-overview" width="300" height="231" /></a>

You can find more about Turbo <a href="http://turbo.net/docs/getting-started" target="_blank">here.</a> (Source)
<h2>Installation:</h2>
Turbo is using a small client which can be downloaded from the website.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-download-client.png" rel="attachment wp-att-3516"><img class="wp-image-3516 size-medium alignnone" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-download-client.png" alt="turbo-download-client" width="300" height="211" /></a>

The setup will automatically install the client and an authentication screen will pop up. When logged in a toolbar is opened with the interface and you are ready to use Turbo.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-logon-client.png" rel="attachment wp-att-3517"><img class="size-medium wp-image-3517 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-logon-client.png" alt="turbo-logon-client" width="300" height="282" /></a>
<h2>Creating a VM:</h2>
To get started with Turbo I'm using a simple application named mRemote which is a free remote desktop tool and can be downloaded <a href="http://www.mremoteng.org/" target="_blank">here</a>.

The first step is to create a new VM with a name. Because the VM is totally isolated a mount path is required to create an opening in the container. This way the installation files can be found. This is done from a command prompt. With the --mount parameter the local directory which contains the installation files can be specified. The -n parameter is to provide the VM a name. The command will be like this:
<pre class="lang:default decode:true">turbo new clean --mount=C:\Sources -n=mremote</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-new-clean-vm.png" rel="attachment wp-att-3529"><img class="alignnone size-medium wp-image-3529" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-new-clean-vm.png" alt="turbo-new-clean-vm" width="300" height="111" /></a>

When the VM is created a command prompt is opened that is running inside the VM. The process can be identified as running inside in the VM by the window title bar. The title will contain an ID and the name of the VM. Within the command prompt the mounted location is accessible to start the installer.<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-cmd.png" rel="attachment wp-att-3521"><img class="size-medium wp-image-3521 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-cmd.png" alt="turbo-cmd" width="300" height="152" /></a>

Just like other virtualization solutions it is recommended to disable automatic updates. In my example I used Windows 7 to create the container as you can see based on the theme of mRemote.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-building-mremote.png" rel="attachment wp-att-3533"><img class="alignnone size-medium wp-image-3533" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-building-mremote.png" alt="turbo-building-mremote" width="300" height="196" /></a>

After the installation and configuration is done the command prompt can be closed.

The next step is to commit the changes. A commit is applying the changes in your local copy. This means it is not directly pushed towards the central hub. In a commit the parameter --startup-file needs to be specified which is executed when the VM is started. In order to commit use the following command:
<pre class="lang:default decode:true ">turbo commit mRemote --startup-file=C:\Program Files\mRemoteNG\mRemoteNG.exe mRemote --overwrite</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-commit-vm.png" rel="attachment wp-att-3525"><img class="alignnone size-medium wp-image-3525" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-commit-vm.png" alt="turbo-commit-vm" width="300" height="111" /></a>

The final step is to push the VM:
<pre class="lang:default decode:true">turbo push mRemote</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-push.png" rel="attachment wp-att-3531"><img class="alignnone size-medium wp-image-3531" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-push.png" alt="turbo-push" width="300" height="111" /></a>

After you pushed the VM it will be available on the "hub" website of Turbo. Within the hub additional information can be added to the VM like a icon and description of the application. This is also the location where the VM can be set to private or public.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-website-vm.png" rel="attachment wp-att-3538"><img class="alignnone size-medium wp-image-3538" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-website-vm.png" alt="turbo-website-vm" width="259" height="300" /></a>
<h2>Creating a release:</h2>
When a VM is pushed to the hub it is not directly set as a release. By using the release command the VM can be set to a specific release number.
<pre class="lang:default decode:true">turbo release mremote 1.0</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-release.png" rel="attachment wp-att-3547"><img class="alignnone size-medium wp-image-3547" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-release.png" alt="turbo-release" width="300" height="111" /></a>

Now the VM is marked with a release number it can be pushed to the hub.
<pre class="lang:default decode:true">turbo push mremote:1.0</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-push-release.png" rel="attachment wp-att-3546"><img class="alignnone size-medium wp-image-3546" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-push-release.png" alt="turbo-push-release" width="300" height="111" /></a>

As you can see in my example the mRemote VM is now available as release 1.0.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-website-release.png" rel="attachment wp-att-3548"><img class="alignnone size-medium wp-image-3548" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-website-release.png" alt="turbo-website-release" width="300" height="67" /></a>

<strong>Running a VM:</strong>

In order to run a VM on another machine you need to make sure the client is installed. On the hub of the VM a command is shown to run the VM. I used a different machine which is running Windows 10 to run my mRemote VM. In my example the mRemote VM can be stated by this command:
<pre class="lang:default decode:true">turbo run ryan@logitblog.com/mremote</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-run.png" rel="attachment wp-att-3532"><img class="alignnone size-medium wp-image-3532" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-run.png" alt="turbo-run" width="300" height="77" /></a>

As you can see based on the screenshot mRemote is now running on Windows 10.

<a href="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-running-mremote.png" rel="attachment wp-att-3530"><img class="alignnone size-medium wp-image-3530" src="{{site.baseurl}}/assets/images/posts/2016-03-21-hands-on-turbo-containers/turbo-running-mremote.png" alt="turbo-running-mremote" width="300" height="197" /></a>

To easily share VM's, Turbo has created a features to embed public VM's on a website. This allows others to quickly acces VM's like this:

<iframe width="500" height="350" src="https://turbo.net/embed/run/ryan%40logitblog.com/mremote" frameborder="0"></iframe>

<h2>Conclusion:</h2>
I like the concept of the centralized cloud hub and it can be very valuable for small start-ups. For the larger organizations Turbo offers an on-premise solution which allows you to host your own hub. More information including pricing can be found here:
<ul>
 	<li><a href="http://turbo.net/pricing" target="_blank">Cloud based</a></li>
 	<li><a href="http://turbo.net/server" target="_blank">On-premise</a></li>
</ul>
To be honest my hands-on experience is still basic and I started simple to better understand the technology. Application container technology is new for me and this was my first hands-on experience with a container solution and I have to say i'm quite impressed. The look and feel of Turbo is very nice and after I followed the guide I managed to get it running quite quick. It is recommended to use a guide if you are new to Turbo, otherwise you will end up stuck like I had in my first attempts.

I hope this blog post is useful to have a better understand how Turbo works and if you have questions or remarks please leave a comment.