---
layout: post
title: Automated Hyper-V VM snapshots with PowerShell
authors: [ryan]
comments: true
categories: [PowerShell]
tags: [Backup, Hyper-V, Microsoft, Powershell, Script, VM]
image: assets/images/posts/2013-09-28-automated-hyper-v-vm-snapshots-with-powershell/automated-hyper-v-vm-snapshots-with-powershell-feature-image.png
---
In this blogpost I will share a script to automate Hyper-V VM snapshots with PowerShell. My goal of this script is to properly shutdown the virtual machine and create a snapshot and maintain the configured amount of snapshots based on day's. This way I can reduce storage and it is possible to revert to a specific snapshot if the machine has broken down.<!--more-->
<h2>Basic information</h2>
We need some basic information that is stored in three variables. These variables contains the Hyper-V host name, the virtual machine name listed on the host and the amount of snapshots you want to maintain based on day's.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$VirtualMachine = "My-VM"
$HyperVHost = "HVhost.lab.local"
$SnapshotDays = 10</pre>
<strong>Note</strong>: The virtual machine name can be listed by running this Powershell command Get-VM -CompterName $HyperVHost or just check in the Hyper-V Manager.
<h2>Shutdown virtual machine</h2>
in the first section we want to make sure the virtual machine is off. This is done by the Stop-VM which initiates a proper shutdown. Before we provide the command we first check if the virtual machine is running.
<pre class="lang:ps decode:true brush: powershell; gutter: false"># Get VM information
$VM = Get-VM -ComputerName $HyperVHost -Name $VirtualMachine
# Provide shutdown command
IF ($VM.State -ne "Off") 
{
	Write-Host (Get-Date)": Shutdown $VirtualMachine"
	Stop-VM -ComputerName $HyperVHost -Name $VirtualMachine
}</pre>
<h2>Wait until virtual machine is off</h2>
The next step is to wait until the virtual machine is off and that is done by the following loop.
<pre class="lang:ps decode:true brush: powershell; gutter: false"># Wait until VM is off
Write-Host (Get-Date)": Waiting for $VirtualMachine to be Off"

While ((Get-VM -ComputerName $HyperVHost -Name $VirtualMachine).State -ne "Off")
{
	Write-Host "." -NoNewLine
	Start-Sleep -Seconds 5
}

Write-Host " "
Write-Host (Get-Date)": $VirtualMachine is Off"</pre>
<h2>Creating the snapshot</h2>
When the virtual machine is off the snapshot is taken. The format I used is the name of the virtual machine with the date of the taken snapshot.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Write-Host (Get-Date)": Creating snapshot of $VirtualMachine"
$DateFormat = Get-Date -Format yyyy.MM.dd
$Snapshot = $VM.Name + "-" + $DateFormat

CheckPoint-VM -ComputerName $HyperVHost -Name $VirtualMachine -Snapshotname $Snapshot 
Start-Sleep -Seconds 2</pre>
<h2>Removing the old snapshots</h2>
This is done by collecting the snapshots older then the configured day's. After these are collected the snapshots are removed by the Remove-VMSnapshot command.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Write-Host (Get-Date)": Removing snapshots of $VirtualMachine older than $SnapshotDays"

Remove-VMSnapshot (Get-VMSnapshot -ComputerName $HyperVHost -VMName $VirtualMachine | Where-Object {$_.CreationTime -lt (Get-Date).AddDays(-$SnapshotDays)})</pre>
<h2>Starting the virtual machine</h2>
The last action is to power on the virtual machine. This is done by the Start-VM command.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Write-Host (Get-Date)": Starting $VirtualMachine"
Start-VM -ComputerName $HyperVHost -Name $VM.Name

Write-Host (Get-Date)": Backup script done"</pre>
<h2>The complete script</h2>
<pre class="lang:ps decode:true brush: powershell; gutter: false ">$VirtualMachine = "My-VM"
$HyperVHost = "HVhost.lab.local"
$SnapshotDays = 10

# Get VM information
$VM = Get-VM -ComputerName $HyperVHost -Name $VirtualMachine
# Provide shutdown command
IF ($VM.State -ne "Off") 
{
	Write-Host (Get-Date)": Shutdown $VirtualMachine"
	Stop-Computer -ComputerName $VirtualMachine -Force
}

# Wait until VM is off
Write-Host (Get-Date)": Waiting for $VirtualMachine to be Off"

While ((Get-VM -ComputerName $HyperVHost -Name $VirtualMachine).State -ne "Off")
{
	Write-Host "." -NoNewLine
	Start-Sleep -Seconds 5
}
Write-Host " "
Write-Host (Get-Date)": $VirtualMachine is in Off state"

Write-Host (Get-Date)": Creating snapshot of $VirtualMachine"
$DateFormat = Get-Date -Format yyyy.MM.dd
$Snapshot = $VM.Name + "-" + $DateFormat

CheckPoint-VM -ComputerName $HyperVHost -Name $VirtualMachine -Snapshotname $Snapshot 
Start-Sleep -Seconds 2

Write-Host (Get-Date)": Removing snapshots of $VirtualMachine older than $SnapshotDays"

Remove-VMSnapshot (Get-VMSnapshot -ComputerName $HyperVHost -VMName $VirtualMachine | Where-Object {$_.CreationTime -lt (Get-Date).AddDays(-$SnapshotDays)})

Write-Host (Get-Date)": Starting $VirtualMachine"
Start-VM -ComputerName $HyperVHost -Name $VM.Name

Write-Host (Get-Date)": Backup script done"</pre>
Create a scheduled task that will run the script at a time you want and the snapshots are going to be created. I have scheduled the task to run every night at 3.00 AM so there are no interruptions on the server during production time.

I hope this is helpful for you and if you have any questions please leave a comment below.
