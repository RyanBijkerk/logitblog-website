---
layout: post
title: Internet in VM's with Hyper-V & Windows 8
authors: [ryan]
comments: true
categories: [Microsoft]
tags: [Hyper-V, Network, Switch, Windows 8]
image: assets/images/posts/2013-02-12-internet-in-vms-with-hyper-v-windows-8/internet-in-vms-with-hyper-v-windows-8-feature-image.png
---
With the introduction of Windows 8 the great feature Hyper-V is added. I have noticed that some people are struggling with their network settings in their virtual machines on Hyper-V. In this blog post I will share my Hyper-V network setup on my laptop. I hope this blog post will help you if you are having problems with your Hyper-V network.

To be flexible it is common to use a WiFi network adapter to give your virtual machines internet access. But what if no WiFi is available or the WiFi is unstable? You can change the Hyper-V switch to the LAN adapter but it is not practical.

<a href="{{site.baseurl}}/assets/images/posts/2013-02-12-internet-in-vms-with-hyper-v-windows-8/Virtual_Switch_Manager.png"><img class="alignleft size-medium wp-image-2158" src="{{site.baseurl}}/assets/images/posts/2013-02-12-internet-in-vms-with-hyper-v-windows-8/Virtual_Switch_Manager.png" alt="" width="300" height="282" /></a>To always get internet on the virtual machines I have bridged my network adapters. This way my virtual machine will get access by WiFi or LAN and I don't have to choose. To accomplish this I have created a internal switch and give it a name for external use.

In the screenshot you can see I have created 2 virtual switches. The external virtual switch is used to share internet on my virtual machines.

<a href="{{site.baseurl}}/assets/images/posts/2013-02-12-internet-in-vms-with-hyper-v-windows-8/Network_Brigde_Properties.png"><img class="size-medium wp-image-2156 alignright" src="{{site.baseurl}}/assets/images/posts/2013-02-12-internet-in-vms-with-hyper-v-windows-8/Network_Brigde_Properties.png" alt="" width="228" height="300" /></a>I have bridged the "vEthernet (External)" with my LAN and WiFi adapter. When I attach the "External" virtual switch to a virtual machine I will have internet access.

When you have any questions, comments or a better method please let me know by leaving a comment.