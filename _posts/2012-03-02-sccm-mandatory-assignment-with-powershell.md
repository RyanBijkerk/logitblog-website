---
layout: post
title: SCCM Mandatory Assignment with Powershell
authors: [ryan]
comments: true
categories: [SCCM]
tags: [Advertisement, Mandatory Assignment, Powershell, SCCM, Scripting]
image: assets/images/posts/2012-03-02-sccm-mandatory-assignment-with-powershell/sccm-mandatory-assignment-with-powershell-feature-image.png
---
I had to automate some steps in SCCM. Therefore I created a GUI based tool with powershell to import applications in SCCM. It also creates advertisements for each application. My customer requires mandatory assignment, based on the date and time in the advertisement of the application. I wrote the following code to create an advertisement with a mandatory assignment.
Some variables are required
<pre class="lang:ps decode:true brush: powershell; gutter: false">$AppName = Application name
$Site = SCCM Site
$CollectionID = The ID from the collection
$PackageID = The ID from the package
$SCCMServer = SCCM Server Name.</pre>
<pre class="lang:ps decode:true brush: powershell; gutter: false ">$Program = Get-WmiObject -ComputerName $Computer -Namespace "root\SMS\Site_$Site" -Query "Select * from SMS_Program WHERE PackageID = '$packageID'"

# Get the current date + time
$Format = get-date -format yyyyMMddHHmmss
$Date = $Format + ".000000+***"

# Create Assigment Time
$sceduleTime = ([WMIClass] "\\$Computer\root\sms\site_$site`:SMS_ST_NonRecurring").CreateInstance()

$sceduleTime.DayDuration = 0
$sceduleTime.HourDuration = 0
$sceduleTime.MinuteDuration = 0
$sceduleTime.IsGMT = "false"
$sceduleTime.StartTime = $Date

# Create Advertisment
$Advertisement = ([WMIClass] "\\$Computer\root\sms\site_$site`:SMS_Advertisement").CreateInstance()

$Advertisement.AdvertFlags = "33685504";
$Advertisement.AdvertisementName = $AppName;
$Advertisement.CollectionID = $CollectionID;
$Advertisement.PackageID = $packageID;
$Advertisement.AssignedSchedule = $sceduleTime;
$Advertisement.DeviceFlags = 0x01000000;
$Advertisement.ProgramName = $Program.ProgramName;
$Advertisement.RemoteClientFlags = "72";
$Advertisement.PresentTime = $Date;
$Advertisement.SourceSite = $Site;
$Advertisement.TimeFlags = "24593"

# Apply Advertisement
$Advertisement.put()</pre>
To get the correct AdvertFlags, DeviceFlags and RemoteClientFlags, create an advertisement with your requirements and collect the settings from the WMI.

<strong>NOTE</strong>: First test the script in a test environment before running in production. Use at your own risk!

If you have a question or suggestion, leave a comment.
