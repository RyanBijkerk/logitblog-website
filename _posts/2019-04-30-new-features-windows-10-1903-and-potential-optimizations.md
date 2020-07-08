---
layout: post
hidden: false
title: New features Windows 10 1903 and potential optimizations
authors: [ryan]
categories: [Windows 10]
tags: [VDI, Windows 10, Windows 10 1903]
image: assets/images/posts/2019-04-30-new-features-windows-10-1903-and-potential-optimizations/new-features-windows10-1903-and-potential-optimizations-feature-image.png
---
This month Microsoft released the new version of Windows, release 1903. This release comes with new features and improvements. This blog post will cover some new features and the potential optimizations in a VDI scenario.

New features in Windows 10 1903
-------------------------------

There are a ton of new features and improvements added in the Windows 10 1903 release. This show Microsoft is dedicated to the development of Windows 10. Let’s highlight a couple new features and improvements.

### Windows Sandbox

Windows Sandbox is a new feature designed to safely run untrusted applications in an isolated environment without harming your device. The feature uses hardware virtualization and the [Microsoft](https://www.microsoft.com/en-us/store/b/home) Hypervisor technology to create a lightweight environment (using around 100MB of space) to install and run an untrusted application. It’s a virtualized environment, but you don’t need to create a virtual machine manually.

### Storage Sense

The Storage settings page has been redesigned to see at glance how space is been utilized, instead of having to select each drive to see the same information, which wasn’t very intuitive for many users. As a result of this change, you’ll no longer find the “Storage sense” section, but you can still access the settings to enable, disable, and configure the feature by clicking the **Configure Storage Sense or run it now**link under “Storage.”

### Light theme

Starting with Windows 10 version 1903, there’s a new system light theme that brings lighter colors for the Start menu, Action Center, taskbar, touch keyboard, and other elements that didn’t have a true light color scheme when switching from the dark to light system theme.

### Acrylic effect

Windows 10 is adding an acrylic effect, part of Fluent Design, to the sign-in background. The translucent texture helps you focus on the login task by moving the actionable controls up in the visual hierarchy while maintaining their accessibility.

For all the details of the new features and improvements please see the [blog post.](https://pureinfotech.com/windows-10-1903-19h1-april-2019-update-features/)

Source: [https://pureinfotech.com/windows-10-1903-19h1-april-2019-update-features/](https://pureinfotech.com/windows-10-1903-19h1-april-2019-update-features/)

Potential Windows 10 1903 optimizations
---------------------------------------

When deploying Windows 10 in a VDI environment optimizing the operating system is one of the best practices. With every new release, there are new optimizations that are introduced by the new features. Tools like the Citrix Optimizer does not hold the new Windows 10 1903 template yet, as this is scheduled for the official Semi-Annual Channel release, which is in a couple of months. For now, it is recommended to use the Windows 10 1809 template, or create your own.

Optimizations are often done by disabling various scheduled tasks, services and removing AppX packages from Windows 10. So let’s compare the difference between Windows 10 1809 and 1903. The following compare are based on full deployments using Citrix VDA 1811.1 and Microsoft Office 2016.

[![](./New%20features%20Windows%2010%201903%20and%20potential%20optimizations%20-%20Logit%20Blog_files/compare-windows-1809-1903.png)](./New%20features%20Windows%2010%201903%20and%20potential%20optimizations%20-%20Logit%20Blog_files/compare-windows-1809-1903.png)

Let’s break this down in the unique added differences between Windows 10 1809 and 1903.

### Scheduled tasks

||
|**Task Name**|**State**|**URI**|
|DirectXDatabaseUpdater|Ready|\\Microsoft\\OneCore\\DirectX\\ DirectXDatabaseUpdater|
|RecommendedTroubleshootingScanner|Ready|\\Microsoft\\Windows\\Diagnosis\\ RecommendedTroubleshootingScanner|
|ReconcileFeatures|Ready|\\Microsoft\\Windows\\Flighting\\ FeatureConfig\\ReconcileFeatures|
|MaintenanceTasks|Ready|\\Microsoft\\Windows\\StateRepository\\ MaintenanceTasks|
|Universal Orchestrator Start|Ready|\\Microsoft\\Windows\\UpdateOrchestrator\\ Universal Orchestrator Start|
|sihpostreboot|Ready|\\Microsoft\\Windows\\WindowsUpdate\\ sihpostreboot|
|Device-Sync|Disabled|\\Microsoft\\Windows\\Workplace Join\\ Device-Sync|

Based on all the task names all listed items are potential optimizations.

### Services

||
|**Display Name**|**Name**|**Status**|**Start Type**|
|Agent Activation Runtime\_7fc0c|AarSvc|Stopped|Manual|
|Cellular Time|autotimesvc|Stopped|Manual|
|CredentialEnrollmentManagerUserSvc\_7fc0c|CredentialEnrollmentManagerUserSvc|Stopped|Manual|
|Citrix Local User Service Manager|CtxLocalUserSrv|Running|Automatic|
|DeviceAssociationBroker\_7fc0c|DeviceAssociationBrokerSvc|Stopped|Manual|
|Display Policy Service|DispBrokerDesktopSvc|Running|Automatic|
|Recommended Troubleshooting Service|TroubleshootingSvc|Stopped|Manual|

Apparently, the Citrix VDA installation behaves differently on Windows 10 1903 installing an extra service named “Citrix Local User Service Manager”. Both installations are done using the same package within MDT and therefore the same command line parameters.

It doesn’t like there are critical services listed so these also include as the potential optimizations.

### AppX Packages

Besides, the AppX package count is exactly the same, there are two new packages.

||
|**Name**|**Version**|**Package Family Name**|
|Microsoft.Windows.StartMenuExperienceHost|10.0.18362.1|Microsoft.Windows.StartMenuExperienceHost\_ cw5n1h2txyewy|
|Microsoft.Windows.CallingShellApp|1000.18362.1.0|Microsoft.Windows.CallingShellApp\_ cw5n1h2txyewy|

Based on the name it would not be recommended to remove the Microsoft.Windows.StartMenuExperienceHost as this may break the start menu. Microsoft.Windows.CallingShellApp package is one that probably can be removed.

> **Note:** please ensure to validate all optimizations to avoid breaking applications or even Windows.

Conclusion
----------

Microsoft Windows 10 1903 comes with a lot of new improvements and functionalities showing the dedication from Microsoft improving Windows 10. In a VDI environment, the optimizations are important and this blog post showed new scheduled tasks, services, and AppX packages.

With every Windows 10 release, the optimizations must be adjusted to get the optimal performance. Ensure to take enough time to develop the new release before deploying into production. And of course, ensure to validate the performance to avoid capacity problems.

Any thoughts on this topic? Leave them below in the comments.

Photo by [Johny vino](https://unsplash.com/photos/R54V69BN0MI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/microsoft?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

