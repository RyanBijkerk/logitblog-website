---
layout: post
title: Capturing VMware ESXtop performance data with PowerShell
authors: [ryan]
comments: true
categories: [VMware]
tags: [ESXtop, Powershell, Powershell, VMware ESX]
image: assets/images/posts/2018-01-18-capturing-vmware-esxtop-performance-data-with-powershell/capturing-vmware-esxtop-performance-data-with-powershell-feature-image.png
---
Performance is a hot topic with the Intel security flaws Meltdown and Spectre. What is the impact of the patch on your environment? Is there enough server capacity available? Performance testing is key, and any valuable data helps to determine the overall impact. In this blog post, I will share how to capture VMware ESXtop data with PowerShell, fully automated!

<!--more-->
<h2>Requirement for SSH</h2>
Like my <a href="http://www.logitblog.com/capturing-citrix-xenserver-performance-data-with-powershell/" target="_blank" rel="noopener">previous post (XenServer)</a> an SSH connection is required to fully automate the data capture. With the <a href="https://github.com/darkoperator/Posh-SSH" target="_blank" rel="noopener">Posh-SSH PowerShell module</a>, we are able to connect to the ESX host and invoke the command to capture the ESXtop data.
<blockquote>Please note: an internet connection is required.

You also can download the package to use it in an offline scenario.</blockquote>
<pre class="lang:ps decode:true ">if (!(Get-PackageProvider | Where-Object {$_.Name -eq "NuGet"})) {
    Write-Host "Default NuGet package provider not installed."
    Write-Host "Installing NuGet package provider."
    Install-PackageProvider -Name "NuGet" -Confirm:$false -Force
    Set-PSRepository -Name "PSGallery" -InstallationPolicy "Trusted" 
}

if (!((Get-Module -ListAvailable *) | Where-Object {$_.Name -eq "Posh-SSH"})) {
    Write-Host "SSH module not found, installing missing module."
    Install-Module -Name Posh-SSH -Confirm:$false -Force
}</pre>
<h2>SSH key</h2>
To create an SSH session to an ESX host requires an SSH key. So before we can start capturing the performance data we need to generate the key. The following <a href="https://kb.vmware.com/s/article/1002866" target="_blank" rel="noopener">knowledge base article</a> explains how to generate the SSH key. In order to add the correct key to the authorized_keys file, you can use the following command. For more information please check <a href="https://blogs.vmware.com/vsphere/2012/07/enabling-password-free-ssh-access-on-esxi-50.html" target="_blank" rel="noopener">this blog post</a>.
<pre class="lang:sh decode:true">cat /root/.ssh/id.dsa.pub | ssh root@&lt;esx host&gt; ‘cat &gt;&gt; /etc/ssh/keys-root/authorized_keys’</pre>
<h2>Capture the ESXtop data</h2>
So right now we have the generated key and should be able to set up an SSH session to the ESX host. There are a couple variables in the script below that need to be assigned. First, it is important to specify the ESX host, username, password and the generated key. The credentials and key will be used to set up the SSH session. The test name will be used as the primary name for the CSV which will contain the ESXtop data. In the example, the data will be captured for 1 hour with an interval of 30 seconds. The CSV file will be captured in the "/tmp/" location. It is recommended to use a PowerShell job to capture the data so other tasks can be executed during the ESXtop capture.

This example captures the default ESXtop data set. More information about ESXtop can be found <a href="http://www.yellow-bricks.com/esxtop/" target="_blank" rel="noopener">here</a>.
<pre class="lang:ps decode:true">$HostName = "Esx01.local.lab"
$EsxUserName = "root"
$EsxPassword = "Password123!"
$EsxKey = "Esx01.key"

$TestName = "Logitblog-exmaple-" + (Get-Date -format 'MMddyy_HHmm').ToString()

$delay = 30
$samples = 120 # * 30 = 3600 sec
$path = "/tmp/$TestName.csv"

$command = "esxtop -b -d $delay -n $samples &gt; $path"

$password = ConvertTo-SecureString $EsxPassword -AsPlainText -Force
$hostCredential = New-Object System.Management.Automation.PSCredential($EsxUserName, $password)

Start-Job -Scriptblock {
    Param(
        [PSCredential]$jobCred,
        [string]$jobHost,
        [string]$jobCommand,
		[string]$jobKey
    )  

    $session = New-SSHSession -ComputerName $jobHost -Credential $jobCred -KeyFile $jobKey -AcceptKey
    Invoke-SSHCommand -Index $session.SessionId -Command $jobCommand
    Get-SSHSession | Remove-SSHSession | Out-Null
} -ArgumentList @($hostCredential, $HostName, $command, $EsxKey)</pre>
<h2>Download the ESXtop data</h2>
Once the capture is done it is useful to automatically download the CSV file. The Posh-SSH module comes with the Get-SCPFile command which allows you to download the file. The following example will download the CSV file to %tmp%.
<pre class="lang:ps decode:true">$localFile = "$env:Temp\" + $TestName + ".csv"
Get-SCPFile -HostName $HostName -RemoteFile $path -LocalFile $localFile -AcceptKey -KeyFile $EsxKey -Credential $hostCredential</pre>
<blockquote><b><i>A quick tip!</i></b><span style="display: inline !important; float: none; background-color: transparent; color: #777777; font-family: 'Open Sans',Helvetica,Arial,sans-serif; font-size: 14.86px; font-style: italic; font-variant: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: left; text-decoration: none; text-indent: 0px; text-transform: none; -webkit-text-stroke-width: 0px; white-space: normal; word-spacing: 0px;"> With the PPD you can quickly create charts in Microsoft Excel out of the ESXtop performance data! More info </span><a href="http://www.logitblog.com/tooling/parse-performance-data/" target="_blank" rel="noopener">here</a><span style="display: inline !important; float: none; background-color: transparent; color: #777777; font-family: 'Open Sans',Helvetica,Arial,sans-serif; font-size: 14.86px; font-style: italic; font-variant: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: left; text-decoration: none; text-indent: 0px; text-transform: none; -webkit-text-stroke-width: 0px; white-space: normal; word-spacing: 0px;">.</span></blockquote>

<h2>Conclusion</h2>
To do proper performance research you need to have valuable data. It is common to include the performance data from a hypervisor like VMware ESX. With PowerShell, you are able to start and download the data capture fully automated. This way you can automate your test process to have consistent data. If you have any comments or questions, please leave them below.

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/oZPwn40zCK4?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Thomas Kvistholt</a> on <a href="https://unsplash.com/search/photos/server?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>