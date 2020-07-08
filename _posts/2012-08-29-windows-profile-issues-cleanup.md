---
layout: post
title: Windows Profile Issues / CleanUp
authors: [ryan]
comments: true
categories: [Windows]
tags: [Corrupt Profiles, GPO, Group Policy, Powershell, SCCM, User Profiles, Windows 7]
image: assets/images/posts/2012-08-29-windows-profile-issues-cleanup/windows-profile-issues-cleanup-feature-image.png
---
I had an issue with corrupted user profiles on a Windows 7 environment with mandatory profiles.

When the environment was build, the group policy setting "Delete cached copies of roaming profiles" was configured. Over a period of time there were some issues with the profiles stored on the desktops. Some applications keeps files locked during the log off process. This resulted in corrupted profiles left on the desktops.

When the user logs on, a new profile is created with the active directory extension behind it.

<a href="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_Corrupt.png"><img class="size-medium wp-image-1834 alignnone" src="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_Corrupt.png" alt="" width="300" height="177" /></a>

To resolve this issue I had to reconfigure the group policy and enable "Delete user profiles older than a specified number of days on system restart" with the value set to 1 day. Each profile older than 1 day (24 hours) will be deleted when the machine reboots. The group policy works much better, but all corrupted profiles are still left on the machine and are not deleted by the group policy.

<a href="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_GPO.png"><img class="alignnone size-medium wp-image-1841" title="" src="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_GPO.png" alt="" width="300" height="195" /></a>

Now I have around 1000 machine with corrupted profiles left on the machine. How do I clean up the profile mess? A simple commandline tool called <a href="http://helgeklein.com/free-tools/delprof2-user-profile-deletion-tool/" target="_blank">DelProf2</a> can do the trick. With DelProf2 it is possible to delete all profiles that are stored on the machine. Even the corrupted profiles.

I created a package in SCCM with the DelProf2 executable and added the following command:
<blockquote>DelProf2.exe /U</blockquote>
The parameter /U will run DelProf2.exe unattended with error results. You have to make sure DelProf only runs when no user is logged on. Make sure the option "Only run when no user is logged on" is set.

<a href="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_SCCM.png"><img class="alignnone wp-image-1872" src="{{site.baseurl}}/assets/images/posts/2012-08-29-windows-profile-issues-cleanup/Profiles_SCCM.png" alt="" width="242" height="280" /></a>

If you are also experiencing these kind of problems I hope this post helps you with resolving your issues. If you have any questions, please leave a comment.
