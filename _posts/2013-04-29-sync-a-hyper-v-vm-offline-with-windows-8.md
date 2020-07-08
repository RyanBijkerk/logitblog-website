---
layout: post
title: Sync a Hyper-V VM Offline with Windows 8
authors: [ryan]
comments: true
categories: [PowerShell]
tags: [Concept, Hyper-V, Powershell, Sync, Windows 8]
image: assets/images/posts/2013-04-29-sync-a-hyper-v-vm-offline-with-windows-8/sync-a-hyper-v-vm-offline-with-windows-8-feature-image.png
---
One of the best features of Windows 8 is in my opinion Hyper-V. Before Windows 8 I had Windows Server 2008 R2 running on my laptop only for the Hyper-V feature. I have a server at home that I use for my study, but sometimes I want to get a VM on my laptop so I can use this offline. Therefore I created a little concept that can sync the VM to my laptop and back to the server.

My server is running Windows Server 2012 Core and my laptop Windows 8 both with the Hyper-V feature enabled. In my concept I use a virtual machine that is available on both machines. I recoded a video where I show the syncing between my server and laptop. The recording is playing real time and has not been fast forwarded.

<iframe width="800" height="461" src="http://www.youtube.com/embed/2zlgvYF1ORQ" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Because this is a concept I have hard coded a couple of things. I have specified the remote Hyper-V server (Ryan-Server) and the VM that I want to sync (Seq-Win7). The $Sync variable is set based on local or remote. Local will sync the VM from the server to my local machine and remote will sync the VM back to the server.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$VMServer = "Ryan-Server"
$VMClient = "Seq-Win7"
$Sync = Read-Host "Do you want to sync the local host or sync to the remote host?"</pre>
The following section will set configuration settings. This contains the host configuration, VM ID's and virtual hard disks locations.
<pre class="lang:ps decode:true brush: powershell; gutter: false">$VMServerConfig = Get-VMHost -Computername $VMServer
$VMLocalConfig = Get-VMHost

$RemoteVM = Get-VM -Name $VMClient -ComputerName $VMserver
$LocalVM = Get-VM -Name $VMClient

$RemoteVMLocation = $RemoteVM.ConfigurationLocation -replace ":", "$"
$RemoteVMLocation = "\\$VMServer\$RemoteVMLocation"
$RemoteVMID = $RemoteVM.VMId.GUID
$RemoteVMHD = $RemoteVM.HardDrives.Path -replace ":", "$"
$RemoteVMHD = "\\$VMServer\$RemoteVMHD"

$LocalVMLocation = $LocalVM.ConfigurationLocation
$LocalVMID = $LocalVM.VMId.GUID
$LocalVMHD = $LocalVM.HardDrives.Path</pre>
This is the function to sync the local machine. First the VM's are put in saved state before the content is copied. Based on the VM ID the bin, vsv and virtual hard disk is copied. These files are required to restore the saved state.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Function SyncVM-Local
{
Write-Host "Going to sync $VMClient to the local host"
Write-Host " "
Write-Host "Saving the VM"
If ($RemoteVM.State -eq "Running")
{
	Save-VM -Name $VMClient -Computer $VMserver
}

If ($LocalVM.State -eq "Running")
{
	Save-VM -Name $VMClient
}

Write-Host " "
Write-Host "Copying content, please wait.."

Copy-Item -Path "$RemoteVMLocation\Virtual Machines\$RemoteVMID\$RemoteVMID.bin" -Destination "$LocalVMLocation\Virtual Machines\$LocalVMID\$LocalVMID.bin"
Copy-Item -Path "$RemoteVMLocation\Virtual Machines\$RemoteVMID\$RemoteVMID.vsv" -Destination "$LocalVMLocation\Virtual Machines\$LocalVMID\$LocalVMID.vsv"
Copy-Item -Path $RemoteVMHD -Destination $LocalVMHD

Start-VM -Name $VMClient

Write-Host " "
Write-Host "Done"

VMConnect localhost $VMClient
}</pre>
This function is used to sync from the local machine back to the remote host.
<pre class="lang:ps decode:true brush: powershell; gutter: false">Function SyncVM-Remote
{
Write-Host "Going to sync $VMClient to the remote host"
Write-Host " "
Write-Host "Saving the VM"

If ($RemoteVM.State -eq "Running")
{
	Save-VM -Name $VMClient -Computer $VMserver
}

If ($LocalVM.State -eq "Running")
{
	Save-VM -Name $VMClient
}

Write-Host " "
Write-Host "Copying content, please wait.."

Copy-Item -Path "$LocalVMLocation\Virtual Machines\$LocalVMID\$LocalVMID.bin" -Destination "$RemoteVMLocation\Virtual Machines\$RemoteVMID\$RemoteVMID.bin"
Copy-Item -Path "$LocalVMLocation\Virtual Machines\$LocalVMID\$LocalVMID.vsv" -Destination "$RemoteVMLocation\Virtual Machines\$RemoteVMID\$RemoteVMID.vsv"
Copy-Item -Path $LocalVMHD -Destination $RemoteVMHD

Start-VM -Name $VMClient -ComputerName $VMServer

Write-Host " "
Write-Host "Done..."

VMConnect $VMServer $VMClient
}</pre>
In the concept I used the following If statements for the sync action.
<pre class="lang:ps decode:true brush: powershell; gutter: false ">If ($Sync -eq "local")
{
	SyncVM-Local
}

If ($Sync -eq "remote")
{
	SyncVM-Remote
}</pre>
What would be nice is to create a form that can show all the available VM's and by selecting a VM you can sync it offline. This way it can be more user friendly.

Try it yourself! The complete script is available <a href="http://www.logitblog.com/wp-content/uploads/2013/04/HyperV_Sync_Script.txt" target="_blank">here</a>.
If you have any questions, suggestions or just a comment please leave them in the comments below.
