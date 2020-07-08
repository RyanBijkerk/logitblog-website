---
layout: post
title: Lost Network Connection on W2K8 Server Core / Hyper-V
authors: [ryan]
comments: true
categories: [Powershell]
tags: [Hyper-V, Microsoft, Network, Powershell, Server Core, Windows Server 2008]
image: assets/images/posts/2011-12-20-lost-network-connection-on-w2k8-server-core-hyper-v/lost-network-connection-on-w2k8-server-core-hyper-v-feature-image.png
---
I installed Windows Server 2008 R2 Server Core to create a Hyper-V server for a test environment. After finalizing the settings and configuring the firewall, I established a remote connection to the Hyper-V server.

My first step was to create two virtual switches, one "Internal Network" and an "External Network". The external connection is linked to the physical network card on the server. After applying the "External Network" I lost the connection with the server. I forgot that Hyper-V disables all network bindings except the "Microsoft Virtual Network Switch Protocol". I Googled to find a way to enable the protocols and found "nvspbind.exe" that can be downloaded <a href="http://archive.msdn.microsoft.com/nvspbind" target="_blank">here</a>. With NVSPbind you can reset, disable/enable the network bindings and change the NIC order. It is a very useful tool that can save a lot of time.

To get the name and GUID I used a Powershell script because it gives a better overview.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Get-WmiObject -Namespace "rootcimv2" -Query "select * from Win32_NetworkAdapter WHERE PhysicalAdapter = 'True'" |
Select-Object NetConnectionID, GUID</pre>
<span style="text-decoration: underline;"><strong>NOTE:</strong></span> I run the NVSPbind from a Powershell prompt. To run in a CMD prompt remove the &amp;.

I checked the enabled and disabled network bindings with the following command.
<pre class="lang:ps decode:true brush: powershell; gutter: false">&amp; NVSPbind "Local Area Connection"</pre>
I want to enable all network bindings except IPv6. To accomplish that, I enable the network bindings individual.
<pre class="lang:ps decode:true brush: powershell; gutter: false">&amp; NVSPbind /e "Local Area Connection" ms_netbios
&amp; NVSPbind /e "Local Area Connection" ms_server
&amp; NVSPbind /e "Local Area Connection" ms_msclient
&amp; NVSPbind /e "Local Area Connection" ms_tcpip
&amp; NVSPbind /e "Local Area Connection" ms_lltdio
&amp; NVSPbind /e "Local Area Connection" ms_rspndr</pre>
After running the commands I had my network connection back and was able to remotely access the server.
