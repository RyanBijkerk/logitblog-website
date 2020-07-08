---
layout: post
title: RES 2011 App-V Integration Wasted Opportunity
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, ConfigMgr, Deployment, RES, RES Workspace Manager, SCCM, Windows 7]
image: assets/images/posts/2012-06-26-res-2011-app-v-integration-wasted-opportunity/res-2011-app-v-integration-wasted-opportunity-feature-image.png
---
During an update of an App-V package I discovered a bug in RES Workspace Manager. I will first summarize the software and versions which are used for the deployment. Then I will explain the issue I encountered.

&nbsp;
<blockquote><strong>Operating System:</strong> Windows 7
<strong>Virtualazation Client:</strong> Microsoft App-V 4.6.0.1523
<strong>Workspace Manager:</strong> RES Workspace Manager 2011 SR2 with Fix Pack 5 (9.5.2.5)
And also tested with
<strong>Workspace Manager:</strong> RES Workspace Manager 2011 SR4 with Fix Pack 6 (9.5.4.6)

<strong>Deployment:</strong> Microsoft Configuration Manager 2007 R2</blockquote>
<strong>About the App-V package:</strong>
An App-V package has been upgraded with an application fix and not an update. The shortcut location, name and version are the same because the executable's where not modified and only some parameters where changed. The package name is changed to a higher build "vendor-app-version-lang-release-build".

<strong>About SCCM:</strong>
SCCM is used to deploy the App-V packages. Because the shortcuts are the same the two packages cannot be deployed both to the same machine. Therefore I created an update script which updates an App-V package with SCCM. I will post this script on logitblog.com soon.

The update part is covered by SCCM and has been tested successfully.

<strong>About RES Workspace Manager:</strong>
The shortcuts are published with RES Workspace Manager. Users have to be a member of the application group to receive the correct shortcut. Because I updated the package I create double shortcuts based on the original "production" package and the new updated package. Both versions are linked to the same AD security group. The shortcuts are published in the following folder structure.
<blockquote>"Folder Vendor Name" - "Folder Application Name" - "Shortcut"</blockquote>
<strong>The update theory:
</strong>Users are having the old App-V package deployed on there systems. The shortcut's of this version are published in the start menu and is behaving as normal. When the update process is started by SCCM the new App-V package version will be deployed on the machine. When the SCCM has completed the deployment and the workspace manager is refreshed the old shortcuts will disappear and the new are published. The user can now use the latest App-V package and work happily ever after.

<strong>What happened during testing:</strong>
During testing I discovered a strange behavior how RES Workspace Manager handles the shortcuts while the original "production" version was still deployed on the machine. The shortcuts from the original "production" and the new version were published in the start menu. What the heck! I always thought when an App-V package is not deployed on the machine the shortcut will not be displayed. The "production" App-V package was only deployed on the machine but I saw both shortcuts.

<strong>Troubleshooting:</strong>
So I started investigating how RES Workspace Manager detect if an App-V package is installed or not. <a href="http://nl.linkedin.com/pub/raymon-rahiembaks/4/533/807" target="_blank">Raymon Rahiembaks</a> has provide me with some information about how RES Workspace Manager handles the shortcuts. To exactly know what happens when the "Workspace" is refreshed I used Procmon to monitor the actions. Like Raymon described to me RES Workspace Manager checks the registry for the installed App-V packages. Under "HKLM\Software\Microsoft\Softgrid\4.5\Client\Applications" there is a key with the shortcut name and version combined. This is created by importing the App-V package on the machine.

<a href="{{site.baseurl}}/assets/images/posts/2012-06-26-res-2011-app-v-integration-wasted-opportunity/AppV-Reg1.png">
<img class="alignnone wp-image-1627" src="{{site.baseurl}}/assets/images/posts/2012-06-26-res-2011-app-v-integration-wasted-opportunity/AppV-Reg1.png" alt="" width="761" height="160" /></a>

In this registry tree App-V package information can be found and also the string "DescriptionURL". The value of the string is the location of the cached OSD.

In Procmon it is clearly visible that the executable "Pfwsmgr.exe" from RES Workspace Manager is checking the locations in the registry. So for testing I changed the value of the "DescriptionURL" to OSD.old. This way RES Workspace Manager should be able to find the location and publish the shortcut in the start menu. But to my surprise the shortcut is still available. So I changed the name of the "DescriptionURL" to "DescriptionURL.old". Finally the shortcut is not being displayed in the start menu.

<strong>Better ways to check:</strong>
It is not possible to check the GUID of the cached OSD because this is randomly generated. How to check if the correct package version is installed? The location of the OSD is known and it is possible to read the cached OSD. In the OSD the GUID can be checked and with the GUID you can check which version is installed. Of course it is also possible to check this in the WMI. The App-V information can be found in "root\microsoft\appvirt\client" in classes "Package". Only the version information has to be known in RES Workspace Manager so it can check which version is installed.

<strong>Conclusion:</strong>
It looks like RES Workspace Manager does not check the App-V settings thorough enough. If the Package GUID and Version GUID are known in the RES Workspace Manager database it is possible to check in the WMI or cached OSD which version is available on the machine. I think this is a wasted opportunity for a nice integration of Microsoft App-V. I hope this will be changed in the near future.

I have created a ticket at RES about this scenario. When I receive an answer I'll keep you up to date.
