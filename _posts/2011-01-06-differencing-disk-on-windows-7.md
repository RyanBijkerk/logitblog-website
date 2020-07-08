---
layout: post
title: Differencing Disk on Windows 7
authors: [ryan]
comments: true
categories: [Deployment]
tags: [Deployment, Differencing disk, Diskpart, VDISK, Windows 7]
image: assets/images/posts/2011-01-06-differencing-disk-on-windows-7/differencing-disk-on-windows-7-feature-image.png
---
I was searching for a quick way to restore to a default installation. After a search I found "Differencing Disk". Here is some information about differencing disk from Microsoft TechNet.

A differencing disk is a virtual hard disk you use to isolate changes to a virtual hard disk or the guest operating system by storing them in a separate file. A differencing disk is similar to the Undo Disks feature because both offer a way to isolate changes in case you want to reverse them. However, Undo Disks is associated with a virtual machine and all disks assigned to it, while a differencing disk is associated only with one disk. In addition, Undo Disks is intended to be a shorter-term method of isolating changes.

A differencing disk is associated with another virtual hard disk that you select when you create the differencing disk. This means that the disk to which you want to associate the differencing disk must exist first. This virtual hard disk is called the "parent" disk and the differencing disk is the "child" disk. The parent disk can be any type of virtual hard disk. The differencing disk stores all changes that would otherwise be made to the parent disk if the differencing disk was not being used. The differencing disk provides an ongoing way to save changes without altering the parent disk. You can use the differencing disk to store changes indefinitely, as long as there is enough space on the physical disk where the differencing disk is stored. The differencing disk expands dynamically as data is written to it and can grow as large as the maximum size allocated for the parent disk when the parent disk was created.

For more information about this click <a title="Microsoft TechNet Diff Disk" href="http://technet.microsoft.com/en-us/library/cc720381(WS.10).aspx" target="_blank">here</a>.
<h2>Follow the following instructions to create a differencing disk.</h2>
When you boot with Windows 7 or Windows Server 2008 installation, open the command prompt. (Shift+F10)
You need a partition to store the VDisk. If you don't have one create one with DISKPART.
Create a VDisk with diskpart the following way:
<blockquote>DISKPART
CREATE VDISK FILE="Location" MAXIMUM="Size" TYPE=EXPANDABLE
ATTACH VDISK
CREATE PARTITION PRIMARY
ACTIVE
EXIT
SETUP</blockquote>
Install Windows on the VDISK that's attached.
Ignore the following message:
<img class="alignnone size-medium wp-image-341" title="Error" src="http://www.logitblog.com/wp-content/uploads/2011/01/Error-300x23.png" alt="" width="300" height="23" />
After installing Windows and your main applications, boot from the Windows installation dvd and open the command prompt again.

Check the location of your VDisk and create the differencing disk the following way:
<blockquote>DISKPART
CREATE VDISK FILE="Location" PARENT="Location\Original.vhd"
ATTACH
ACTIVE
EXIT</blockquote>
To make it bootable, create a boot entry the following way:
(the GUID is a unique number that you have to copy)
<blockquote>BCDEDIT /COPY {DEFAULT} /D "Windows 7 (Diff disk 1)"
BCDEDIT /SET {GUID} DEVICE VHD=[C:]\"Location.vhd"
BCDEDIT /SET {GUID} OSDEVICE VHD=[C:]\"Location.vhd"</blockquote>
You can set the VDisk as default.
<blockquote>BCDEDIT /SET {GUID} /DEFAULT</blockquote>
When you boot your system you have two options to boot from; the original installation and the differencing disk. To undo the disk, you have to create a new differencing disk with the original parrent disk. After this you can delete the old differencing disk and the boot entry.
With BCDEDIT you can change the boot order and more. For more information about BCDEDIT click <a title="Microsoft TechNet BCDEDIT" href="http://technet.microsoft.com/en-us/library/cc709667(WS.10).aspx" target="_blank">here</a>.
