---
layout: post
title: Importing App-V apps in SCCM with Powershell
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Powershell, SCCM]
image: assets/images/posts/2012-01-30-importing-app-v-apps-in-sccm-with-powershell/importing-app-v-apps-in-sccm-with-powershell-feature-image.png
---
With the SCCM 2007 R2 release Microsoft delivers a VBScript to import and manage App-V applications. Because the limitations I wrote the import part in Powershell. The following script is only to import an App-V application and does not create a Collection or Advertisement.

To import an App-V application the following parameters are required.

$App = The App-V sequence name based on the directory name.
$Site = SCCM Site
$smsShare = The share of the SCCM server to copy the application to.
$AppShare = The share where the application is located.
$SCCMServer = SCCM Server Name.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Param($App, $Build, $Site, $smsShare, $AppShare, $SCCMServer)</pre>
The following function is to add the icon in SCCM.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Function Icon-Handeling
{
param($Icon)

$Obj = New-Object -ComObject ADODB.Stream
$Obj.Open()
$Obj.Type = 1
$Obj.LoadFromFile("$Icon")

Set-Variable -Name IconData -Value $Obj.Read() -Scope 2
}</pre>
<!--more-->

Create a new directory based on the App-V application name and copy the application  from the source to the share.
<pre class="lang:ps decode:true brush: powershell; gutter: false">New-Item  "$smsShare\$App\" -Type Directory -Force
$Destination = "$smsShare\$App\"

Copy-Item $AppShare\* -Destination $Destination -Recurse -Force</pre>
Get the content from the manifest file of the App-V application.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$Manfst = Get-ChildItem "$smsShare\$App\*.xml" -Name
[xml]$Manifest = get-content "$smsShare\$App\$Manfst"

$Name = $Manifest.Package.Name
$GUID = $Manifest.Package.GUID
$Version = $Manifest.Package.VersionGuid

$Commandline = "PkgGUID=" + "$GUID" + ":VersionGUID=" + "$Version"</pre>
Create the extended data variable.
<pre class="lang:default decode:true brush: powershell; gutter: false">$exData = [Text.Encoding]::UTF8.GetBytes($Version)
$exDataSize = $exData.getupperbound(0) + 1</pre>
Create an argument array for the package and apply the WMI data.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$argumentsPackage = @{Name = "$Name";
Manufacturer = "";
ExtendedData = $exData;
ExtendedDataSize = $exDataSize;
Version = "";
Language = "";
PackageType = 7;
PkgFlags = 104857600;
PkgSourceFlag = 2;
PkgSourcePath = "$smsShare\$App\"
}

$SetPkg = Set-WmiInstance -Computername $SCCMServer -class SMS_Package -arguments $argumentsPackage -namespace "root\SMS\Site_$Site"</pre>
Collect the Package ID that is created by SCCM.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$Package = Get-WmiObject -Computername $SCCMServer -Namespace "root\SMS\Site_$Site" -Query "Select * from SMS_Package WHERE Name = '$App'"
$PackageID = $Package.PackageID</pre>
Rename the "sft" file to the package ID and remove the "sprj" file.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Rename-Item "$smsShare\$App\$App.sft" "$PackageID.sft"
Remove-Item "$smsShare\$App\$App.sprj" -Force</pre>
Create an argument array for the program and apply the WMI data.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$argumentsProgram = @{
PackageID = $Package.PackageID;
ProgramFlags = "135307273";
ProgramName = "[Virtual application]";
CommandLine = "$Commandline"
}

$SetPrg = Set-WmiInstance -Computername $SCCMServer -class SMS_Program -arguments $argumentsProgram -namespace "root\SMS\Site_$Site"</pre>
All server distribution points that are Windows NT Server and has the role SMS Distribution Point are added to the an array.

Create an argument array for the distribution points and apply the WMI data.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$Distribution = Get-WmiObject -ComputerName $SCCMServer -Namespace "root\SMS\Site_$Site" -Query "Select * from SMS_SystemResourceList Where RoleName='SMS Distribution Point' and ResourceType='Windows NT Server'"

$i = 0

IF ($Distribution.count -like "") {

$argumentsDstr = @{
PackageID = $Package.packageID;
ServerNALPath = $Distribution.NALPath;
	}
		$SetDstr = Set-WmiInstance -Computername $SCCMServer -class SMS_DistributionPoint -arguments $argumentsDstr -namespace "root\SMS\Site_$Site"
} ELSE { DO {
$Setting = $Distribution[$i] ; $i++

$argumentsDstr = @{
PackageID = $Package.packageID;
ServerNALPath = $Setting.NALPath;
	}

		$SetDstr = Set-WmiInstance -Computername $SCCMServer -class SMS_DistributionPoint -arguments $argumentsDstr -namespace "root\SMS\Site_$Site"
}	While ($i -le $Distribution.count -1)
}</pre>
Rename the SFT to the Packge ID in the OSD files.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$i = 0

[array]$OSDS = Get-ChildItem "$smsShare\$App\*.osd" -Name

IF ($OSDS.count -like ""){
(Get-Content -Path "$smsShare\$App\$OSDS") -replace "$App.sft" , "$reSFT"  | Set-Content "$smsShare\$App\$OSDS"} ELSE { 

	DO {
		$Location = "$smsShare\$App"
		$FileOSD = $OSDS[$i] ; $i++

		(Get-Content -Path "$Location\$FileOSD") -replace "$App.sft" , "$reSFT"  | Set-Content "$smsShare\$App\$FileOSD"

}While ($i -le $OSDS.count -1)}</pre>
Get the icon data and apply the WMI data for each OSD file.
<pre class="lang:ps decode:true brush: powershell; gutter: false ">$i = 0

IF ($OSDS.count -like ""){

[xml]$OSD = Get-Content "$smsShare\$App\$OSDS"

$PkgGUID = $OSD.Softpkg.GUID
$PkgName = $OSD.Softpkg.Name
$PkgVers = $OSD.Softpkg.Version

IF ($OSD.Softpkg.MGMT_Shortcutlist.Shortcut.count -like "")
{
	$RawIcon = $OSD.Softpkg.MGMT_Shortcutlist.Shortcut.Icon} ELSE {
	$RawIcon = $OSD.Softpkg.MGMT_Shortcutlist.Shortcut[0].Icon
}

$RawIcon = $RawIcon -replace "/", "\"
$Icon = $RawIcon -replace "%SFT_MIME_SOURCE%", "$smsShare\$App"

Icon-Handeling "$Icon"

$IconSize = $IconData.getupperbound(0) + 1

$argumentsApps = @{
GUID = "$PkgGUID";
IconSize = $IconSize;
Icon = $IconData;
PackageID = $Package.PackageID;
Name = "$PkgName";
Version = "$PkgVers"
}

$VApp = Set-WmiInstance -Computername $SCCMServer -class SMS_VirtualApp -arguments $argumentsApps -namespace "root\SMS\Site_$Site"

} ELSE { DO {

$FileOSD = $OSDS[$i] ; $i++
[xml]$OSD = Get-Content "$Location\$FileOSD"

$PkgGUID = $OSD.Softpkg.GUID
$PkgName = $OSD.Softpkg.Name
$PkgVers = $OSD.Softpkg.Version

IF ($OSD.Softpkg.MGMT_Shortcutlist.Shortcut.count -like "")
{
	$RawIcon = $OSD.Softpkg.MGMT_Shortcutlist.Shortcut.Icon} ELSE {
	$RawIcon = $OSD.Softpkg.MGMT_Shortcutlist.Shortcut[0].Icon
}

$RawIcon = $RawIcon -replace "/", "\"
$Icon = $RawIcon -replace "%SFT_MIME_SOURCE%", "$smsShare\$App"

Icon-Handeling "$Icon"

$IconSize = $IconData.getupperbound(0) + 1

$Location = "$smsShare\$App"

$argumentsApps = @{
GUID = "$PkgGUID";
IconSize = $IconSize;
Icon = $IconData;
PackageID = $Package.PackageID;
Name = "$PkgName";
Version = "$PkgVers"
}
$SetVApp = Set-WmiInstance -Computername $SCCMServer -class SMS_VirtualApp -arguments $argumentsApps -namespace "root\SMS\Site_$Site"

} While ($i -le $OSDS.count -1)}</pre>
After these steps the App-V package is available in SCCM. I used WMI explorer to collect specific settings for the packages. Configure the required settings and look for ProgramFlags and PkgFlags values with the WMI explorer.

The script is available in a text file that can be downloaded <a title="App-V import Script" href="http://www.logitblog.com/wp-content/uploads/2012/01/Import-AppV.txt" target="_blank">here</a>.

<strong>NOTE</strong>: First test the script in a test environment before running in production. Use at your own risk!

If you have a question, suggestion or note please leave a comment.
