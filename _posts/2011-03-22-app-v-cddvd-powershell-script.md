---
layout: post
title: App-V CD/DVD powershell script
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Powershell]
image: assets/images/posts/2011-03-22-app-v-cddvd-powershell-script/app-v-cd-dvd-powershell-script-feature-image.png
---
<p style="text-align: justify;">Some applications require a CD/DVD for the licence of the application. This can be a challenge if the application does not check the CD/DVD source. Some applications save the drive settings in an ini file and that isn't very dynamic. When the drive letter of the CD/DVD drive changes the application can't find the source anymore because the INI file doesn't detect the change. I had to sequence an application that required the CD and save the source settings in an ini file that is located in the Windows directory. This application has to be dynamic with the different drive letters because the application is deployed to different hardware set-ups. To make this dynamic I created a powershell script that checks all logicaldisks on the volumename and drivetype. When there is no CD/DVD that contains the volumename and correct drivetype the script displays an error message in a messagebox.</p>
<p style="text-align: justify;">This way the application is dynamic with diffrent drive letters. In my case the application has a file type association (FTA) that also has to be compatible with the dynamic drive letters. To realise this I added a parameter in the script to open the file with the right FTA. If a different file has opened the script will display an error, saying that the FTA is not compatible with the application.</p>

<blockquote><code>param($Arg1)
$ErrorActionPreference = <span style="color: #888888;">"SilentlyContinue"</span>
trap {
</code><code><span style="color: #339966;">
# Global error</span>
[VOID][System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms")
[Windows.Forms.MessageBox]::Show(<span style="color: #888888;">"<span style="color: #ff0000;">Could not continue.</span>"</span>,
<span style="color: #888888;">"<span style="color: #ff0000;">APP Error</span>"</span>,
[Windows.Forms.MessageBoxButtons]::Ok,
[Windows.Forms.MessageBoxIcon]::exclamation)
}
</code>
<code><span style="color: #339966;"># Function to get the CD/DVD drive letter</span></code>
<code><span style="color: #3366ff;">function </span>Get-CDDrives
{
@(<span style="color: #993366;">Get-WmiObject</span>win32_logicaldisk | <span style="color: #993366;">where-Object</span>{$_.VolumeName -eq "APP"} | <span style="color: #993366;">where-Object </span>{$_.Drivetype -eq <span style="color: #888888;">"5"</span>} | ForEach-Object { $_.DeviceID})
}
</code>
<code><span style="color: #339966;"># Opens the application with FTA</span></code>
<code><span style="color: #3366ff;">function </span>Open-param
{
(<span style="color: #993366;">get-content</span> "<span style="color: #888888;"><span style="color: #ff0000;">$env:windir\APP.ini</span>"</span>) -replace <span style="color: #888888;">"<span style="color: #ff0000;">DataDir=.*</span>"</span>, <span style="color: #888888;">"<span style="color: #ff0000;">DataDir=$DRIVE</span>"</span> | <span style="color: #993366;">set-content</span> <span style="color: #888888;">"<span style="color: #ff0000;">$env:windir\APP.ini</span>"</span>
</code>
<code>&amp; <span style="color: #888888;">"<span style="color: #ff0000;">Q:\APP.101\Application\App.exe</span>" "$ARG1"</span>
}
</code>
<code><span style="color: #339966;"># Opens the application normal</span>
<span style="color: #3366ff;">function </span>Open-norm
{
(<span style="color: #993366;">get-content</span> <span style="color: #888888;">"<span style="color: #ff0000;">$env:windir\APP.ini</span>"</span>) -replace <span style="color: #888888;">"<span style="color: #ff0000;">DataDir=.*</span>"</span>, <span style="color: #888888;">"<span style="color: #ff0000;">DataDir=$DRIVE</span>"</span> | <span style="color: #993366;">set-content</span> <span style="color: #888888;">"<span style="color: #ff0000;">$env:windir\APP.ini</span>"</span>
</code>
<code>&amp; <span style="color: #888888;">"<span style="color: #ff0000;">Q:\APP.101\Application\App.exe</span>"</span>
}
</code>
<code><span style="color: #339966;"># Error no disc</span></code>
<code><span style="color: #3366ff;">function </span>Open-error
{
[VOID][System.Reflection.Assembly]::LoadWithPartialName(<span style="color: #888888;">"System.Windows.Forms"</span>)
[Windows.Forms.MessageBox]::Show(<span style="color: #888888;">"<span style="color: #ff0000;">Please insert the application CD and start the application again.</span>"</span>,
<span style="color: #888888;">"<span style="color: #ff0000;">APP message</span>"</span>,
[Windows.Forms.MessageBoxButtons]::Ok,
[Windows.Forms.MessageBoxIcon]::exclamation)
}
</code>
<code>IF (!$ARG1)
{
$TEMP = Get-CDDrives
$DRIVE = $TEMP + <span style="color: #888888;">"\"</span>
</code>
<code><span style="color: #3366ff;">IF </span>($DRIVE -eq <span style="color: #888888;">"\"</span>) {Open-error}
<span style="color: #3366ff;">ELSE </span>{Open-norm}
} <span style="color: #3366ff;">ELSEIF </span>($ARG1 -like <span style="color: #888888;">"<span style="color: #ff0000;">*.extension</span>"</span>)
{
$TEMP = Get-CDDrives
$DRIVE = $TEMP + <span style="color: #888888;">"\"</span>
<span style="color: #3366ff;">IF </span>($DRIVE -eq <span style="color: #888888;">"\"</span>) {Open-error}
<span style="color: #3366ff;">ELSE </span>{Open-param}
</code>
<code>} <span style="color: #3366ff;">ELSE</span>
{
<code><span style="color: #339966;">
# Error wrong FTA</span></code>
[VOID][System.Reflection.Assembly]::LoadWithPartialName(<span style="color: #888888;">"System.Windows.Forms"</span>)
[Windows.Forms.MessageBox]::Show(<span style="color: #888888;">"<span style="color: #ff0000;">Could not continue, please make sure you're opening the right file.</span>"</span>,
<span style="color: #888888;">"<span style="color: #ff0000;">APP Error</span>"</span>,
[Windows.Forms.MessageBoxButtons]::Ok,
[Windows.Forms.MessageBoxIcon]::exclamation)
}</code></blockquote>
<p style="text-align: justify;">Change the red highlighted lines to your application and error messages. When you're sequencing the application open a powershell prompt and run "set-executionpolicy remotesigned" or just add a reg file with the right settings. When you are at step 4 of 6 replace the application path of the application with the following line:</p>
<p style="text-align: justify;">"C:\Windows\System32\WINDOW~1\v1.0\Powershell.exe" -WindowStyle hidden "Q:\Youre script location.ps1". Make sure you use the 8.3 file names. Add a FTA and use the parameter %1. Finish the sequence and use the application.</p>
<p style="text-align: justify;">If you have a suggestion or question, leave a comment.</p>
