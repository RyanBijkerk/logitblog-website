---
layout: post
title: Capturing Citrix XenServer performance data with PowerShell
authors: [ryan]
comments: true
categories: [Citrix]
tags: [Automation, Citrix, Powershell, PPD, XenServer]
image: assets/images/posts/2017-08-23-capturing-citrix-xenserver-performance-data-with-powershell/capturing-citrix-xenserver-performance-data-with-powershell-feature-image.png
---
Capturing performance data is important to fully understand the impact of changes. With automation, you can guarantee a consistent data capture which is important to compare different scenarios. In this blog post, I'm going to share how to capture Citrix XenServer performance data with PowerShell.

<h2>Requirments for SSH</h2>
In order to fully automate the capturing, you need a way to connect to the XenServer host using SSH. This can be done with the PowerShell module Posh-SSH which is required for the examples below. You can install the module manually or use the following example to install the module automatically.
<blockquote><strong>Please note:</strong> an internet connection is required.</blockquote>
<pre class="lang:ps decode:true">if (!(Get-PackageProvider | Where-Object {$_.Name -eq "NuGet"})) {
    Write-Host "Default NuGet package provider not installed."
    Write-Host "Installing NuGet package provider."
    Install-PackageProvider -Name "NuGet" -Confirm:$false -Force
    Set-PSRepository -Name "PSGallery" -InstallationPolicy "Trusted" 
}

if (!((Get-Module -ListAvailable *) | Where-Object {$_.Name -eq "Posh-SSH"})) {
    Write-Host "SSH module not found, installing missing module."
    Install-Module -Name Posh-SSH -Confirm:$false -Force
}</pre>
<h2>Capturing the XenServer data</h2>
To capture the performance data a couple of variables needs to be assigned. First, it is important to specify the XenServer host, username and password. These will be used to connect to the XenServer host using SSH. The testname will be used as the primary name for the CSV file. Consistent data is important so you can compare multiple scenarios. The timeout and delay are used to define the total time to capture the data and the interval. In the example below data is captured for 10 minutes with a 30 seconds interval. I'm always using jobs to capture performance data because this allows me to do other tasks during the capture.

RRD2CSV is the primary tool for capturing the performance data. More information can be found <a href="http://www.florisvanderploeg.com/gathering-performance-data-in-xenserver-for-external-parsing/" target="_blank" rel="noopener">here</a>.
<pre class="lang:ps decode:true">$HostName = "Xen01.local.lab"
$XenUserName = "root"
$XenPassword = "Password123!"

$TestName = "Logitblog-exmaple-" + (Get-Date -format 'MM.dd.yy_HH.mm').ToString()
$TimeOut = 600

$delay = 30
$path = "/root/$TestName.csv"

$command = "timeout $($TimeOut)s rrd2csv -s $delay &gt; $path"

$password = ConvertTo-SecureString $XenPassword -AsPlainText -Force
$hostCredential = New-Object System.Management.Automation.PSCredential ($XenUserName, $password)

Start-Job -Scriptblock {
    Param(
        [PSCredential]$jobCred,
        [string]$jobHost,
        [string]$jobCommand
    )  

    $session = New-SSHSession -ComputerName $jobHost -Credential $jobCred -AcceptKey
    Invoke-SSHCommand -Index $session.SessionId -Command $jobCommand
    Get-SSHSession | Remove-SSHSession | Out-Null
} -ArgumentList @($hostCredential, $HostName, $command)</pre>
<h2>Downloading the data from XenServer</h2>
In order to analyze the results, you need to download the performance data from the XenServer host. This can also be done using the Posh-SSH module! Using the Get-SCPFile you are able to download the file to your local disk.
<pre class="lang:default decode:true">$localFile = "$env:Temp\" + $TestName + ".csv"
Get-SCPFile -HostName $HostName -RemoteFile $path -LocalFile $localFile  -Credential $hostCredential</pre>
<blockquote><strong>A quick tip!</strong> With the PPD you can quickly create charts in Microsoft Excel out of the XenServer performance data! More info <a href="http://www.logitblog.com/tooling/parse-performance-data/" target="_blank" rel="noopener">here</a>.</blockquote>
<h2>Conclusion</h2>
With the examples above you are able to fully automate capturing and downloading performance data from XenServer. Thanks to the module Posh-SSH this can be done with PowerShell. Using this method is a consistent way to capture the data so you can compare different scenarios. I hope this blog post is useful and if you have any comments or questions please leave them below.

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/ijXhTL3_VfA?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Chris Holgersson</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
