---
layout: post
title: MDT Storage Driver not found on VMware
authors: [ryan]
comments: true
categories: [Automation]
tags: [Deployment, Development, MDT, Powershell, VMware]
image: assets/images/posts/2016-09-20-mdt-storage-driver-not-found-on-vmware/mdt-storage-driver-vmware-feature-image.png
---
When a reference image is up to date with the latest Windows updates, the deployment time will decrease tremendously. <a href="http://xenappblog.com/2014/automated-master-image-creation/" target="_blank">Trond Eirik Haavarstein </a>has written an excellent blog post on how to create a reference image with Microsoft Deployment Toolkit (MDT). During my deployment in a VMware environment, I encountered the following error: "Disk(0) was not found. Unable to continue. Possible cause: Missing storage drivers". In this blog post, I share how to solve this error message.
<h2>Why updating my reference image?</h2>
When hosting a Virtual Desktop environment, like a XenDesktop, RDS or VMware Horizon, it is very important to make sure all the machines are up to date with the Windows updates. Windows updates are one of the performance tuning recommendations. In order to deliver the best end-user experience, Windows updates are required. Updating a golden image manually is time-consuming and it is a best practice to automatically create your golden images.
<h2>Creation of the VM for the reference image</h2>
Like the blog post mentioned above the creation of the reference image is automated. I'm using a PowerShell script to create a VM. When the VM is created the MAC address is retrieved and updated in the CustomSettings.ini from MDT. This way the task sequence will automatically run when booting from the MDT iso.

Using the following VMware PowerCLI command the virtual machine is created.
<pre class="lang:ps decode:true">New-VM -Name $vmName -ResourcePool $selectedHost -Datastore $selectedDataStore -NumCPU 2 -MemoryGB 4 -DiskGB 32 -CD -DiskStorageFormat Thin</pre>
<h2>Missing storage driver error</h2>
By default within a task sequence a format and partition disk is defined and during execution of this step the following error occurs:

Disk(0) was not found.  Unable to continue
Possible cause: Missing storage drivers

<a href="{{site.baseurl}}/assets/images/posts/2016-09-20-mdt-storage-driver-not-found-on-vmware/mdt-storage-driver-error.png"><img class="alignnone size-medium wp-image-3747" src="{{site.baseurl}}/assets/images/posts/2016-09-20-mdt-storage-driver-not-found-on-vmware/mdt-storage-driver-error.png" alt="mdt-storage-driver-error" width="300" height="215" /></a>

As the description explains the disk cannot be found and running the diskpart list disk command will confirm the missing disk.
<h2>How to solve the error</h2>
When creating a virtual machine manually a wizard is forcing you to select a type of operating system. The command used above for creating a VM does not have any operating system defined. This can be done with the -GuestId parameter. The description on the <a href="https://www.vmware.com/support/developer/PowerCLI/PowerCLI41/html/New-VM.html">VMware documentation site</a> is as followed:
<table cellspacing="0">
<tbody>
<tr class="r1">
<td>Specify the guest operating system of the new virtual machine. See below the valid values for the various ESX versions. Depending on the hardware configuration of the host, some of the guest operating systems might be inapplicable.</td>
</tr>
</tbody>
</table>
By adding the -GuestId [name] the problem is solved and the disk can be found during the deployment. The full correct command will be like this:
<pre class="lang:ps decode:true">New-VM -Name $vmName -ResourcePool $selectedHost -Datastore $selectedDataStore -NumCPU 2 -MemoryGB 4 -DiskGB 32 -CD -DiskStorageFormat Thin -GuestId windows7_64Guest</pre>
There are many options as guest id and in my case, I used the Windows 7 x64 for all my deployment, which is also working for x86 deployments.
<h2>Conclusion</h2>
Updating your reference image will decrease deployment time. By automating these deployments you never have to look at it. When encountering the missing storage driver in a VMware environment, make sure the -GuestId is specified when creating the VM.

I hope this blog post is useful and if you have any comments or questions please leave them below.
