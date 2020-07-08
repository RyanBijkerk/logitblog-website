---
layout: post
title: 'Hands-on: VMware App Volumes'
authors: [ryan]
comments: true
categories: [VMware]
tags: [App Volumes, CloudVolumes, Deployment, VMware, VMware]
image: assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/hands-on-vmware-app-volumes-feature-image.png
---
I had an invitation to join a session by <a href="https://twitter.com/harrylabana" target="_blank">Harry Labana </a>about the acquisition of <a href="http://immidio.com/" target="_blank">Immidio </a>and how Flex+ will complete the end using computing stack of VMware. During this session the product VMware App Volumes was introduced to me and based on that session I decided to try App Volmues out. In this blog post I will share my hands-on experience.
<h2>Introduction</h2>
Before I start let's explain what VMware App Volume is. App Volumes allows IT to deliver applications and data to users or desktops in seconds, and at scale. Applications are stored in shared read-only virtual disks that instantly attach to desktops by users, groups, or devices. These applications perform like natively installed applications for end users providing a seamless desktop experience. (<a href="http://www.vmware.com/files/pdf/appvolumes/vmware-appvolumes-datasheet.pdf" target="_blank">source</a>)

<a href="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/vmw-dgrm-app-volumes-overview-101.jpg"><img class="alignnone size-medium wp-image-3099" src="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/vmw-dgrm-app-volumes-overview-101.jpg" alt="" width="300" height="225" /></a>
<h2>Installation</h2>
The installation is simple and straight forward. I tried standalone deployment with VHD's and a deployment on a single ESX host. In both scenario's I managed to get the product up and running within a couple of hours. When using a existing SQL server you have to make sure the permissions on the database are set correctly otherwise the web interface will hang. So read the deployment manual when using an existing SQL server.

Within the client machine an agent needs to be installed. During the installation you have to specify the App Volumes server and the port. These can be modified afterwards in the registry on the following location:

"HKEY_LOCAL_MACHINE\SOFTWARE\CloudVolumes\Agent"
<h2>Configuration</h2>
<a href="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/configuration-storage.png"><img class="alignright wp-image-3113 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/configuration-storage.png" alt="" width="300" height="202" /></a>
The standalone deployment I had some difficulties with the VHD templates. I had to manually search for the templates and copy them to the share that I had created. These are stored on the ISO in:

"ISO:\Hypervisor\In-Guest VHD\cloudvolumes".

<a href="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/location-storage.png"><img class="alignnone size-medium wp-image-3112" src="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/location-storage.png" alt="" width="300" height="144" /></a>
<h2>App Stacks</h2>
VMware App Volumes are using "app stacks" which are the virtual disks that contains the applications. Please note when the templates are not available you will not be able to create the app stack. This can be solved by coping the templates to the correct location.

After creating an app stack you have the option to enable provisioning mode and assign it to a machine. With the standalone deployment the VHD is not attached automatically while this works properly with the ESX deployment. This is solved by browsing to the share where the App Stacks are stored and attaching the VHD that is in provisioning mode. After the disk is attached you can start the installation of the application.
<h2>Assigning an app stack</h2>
<a href="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/appstack-assign.png"><img class="alignleft wp-image-3114 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-02-28-hands-on-vmware-app-volumes/appstack-assign.png" alt="" width="300" height="202" /></a>After provisioning an app stack you can assign the app stack to computers or users. With a ESX deployment you have the ability to assign the app stack immediately.

Otherwise the assignment will be done when the user logs on to the machine. But still, that is way faster than a regular application deployment.

<h2>Conclusion</h2>
I'm very impressed with the instant application delivery by VMware App Volumes. It is truly as fast as within the demo which can make deployments extremely easy. By being agile in these deployments you can shorten the application packaging cycles. Because VMware App Volumes can be used in non VMware environments it has a lot of potential. Even for me in the software development it has benefits for being able to quickly switch between applications like Microsoft Office. It works very nice and I really recommend to take a look at VMware App Volumes.
