---
layout: post
title: Change VMware vGPU settings with PowerShell
authors: [ryan]
comments: true
categories: [NVIDIA]
tags: [NVIDIA, Powershell, VMware, VMware]
image: assets/images/posts/2016-07-29-change-vmware-vgpu-settings-powershell/change-vmware-vgpu-settings-with-powershell-feature-image.png
---
Within larger organizations, centralized desktop with graphical performance is becoming a common use case. When building these of environments performance testing is crucial to get optimal user experience. During testing, it can occur you have to change the vGPU settings. Within this blog post, I will share a script that helps you change GPU profiles for multiple machines.
<h2>Which vGPU settings need to be changed?</h2>
When using a vGPU you need to specify a virtual GPU profile. Depending on the type of card you have multiple profiles to choose from depending on the use case. Please see the following overview of available profiles:
<table border="1" width="467" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td width="59">
<p align="center"><b>NVIDIA GRID Graphics Board</b></p>
</td>
<td width="46">
<p align="center"><b>Virtual GPU Profile</b></p>
</td>
<td width="59">
<p align="center"><b>Graphics Memory</b></p>
</td>
<td width="54">
<p align="center"><b>Max Displays Per User</b></p>
</td>
<td width="71">
<p align="center"><b>Max Resolution Per Display</b></p>
</td>
<td width="60">
<p align="center"><b>Max Users Per Graphics Board</b></p>
</td>
<td width="118">
<p align="center"><b>Recommended Use Case</b></p>
</td>
</tr>
<tr>
<td rowspan="4" width="59">GRID K2</td>
<td width="46">K280Q</td>
<td width="59">4 GB</td>
<td width="54">4</td>
<td width="71">2560×1600</td>
<td width="60">2</td>
<td width="118">Designer</td>
</tr>
<tr>
<td width="46">K260Q</td>
<td width="59">2 GB</td>
<td width="54">4</td>
<td width="71">2560×1600</td>
<td width="60">4</td>
<td width="118">Designer/Power User</td>
</tr>
<tr>
<td width="46">K240Q</td>
<td width="59">1 GB</td>
<td width="54">2</td>
<td width="71">2560×1600</td>
<td width="60">8</td>
<td width="118">Designer/Power User</td>
</tr>
<tr>
<td width="46">K220Q</td>
<td width="59">512 MB</td>
<td width="54">2</td>
<td width="71">2560×1600</td>
<td width="60">16</td>
<td width="118">Designer/Power User</td>
</tr>
<tr>
<td rowspan="4" width="59">GRID K1</td>
<td width="46">K180Q</td>
<td width="59">4 GB</td>
<td width="54">4</td>
<td width="71">2560×1600</td>
<td width="60">4</td>
<td width="118">Entry Designer</td>
</tr>
<tr>
<td width="46">K160Q</td>
<td width="59">2 GB</td>
<td width="54">4</td>
<td width="71">2560×1600</td>
<td width="60">8</td>
<td width="118">Power User</td>
</tr>
<tr>
<td width="46">K140Q</td>
<td width="59">1 GB</td>
<td width="54">2</td>
<td width="71">2560×1600</td>
<td width="60">16</td>
<td width="118">Power User</td>
</tr>
<tr>
<td width="46">K120Q</td>
<td width="59">512 MB</td>
<td width="54">2</td>
<td width="71">2560×1600</td>
<td width="60">32</td>
<td width="118">Power User</td>
</tr>
</tbody>
</table>
Source: <a href="http://blogs.vmware.com/euc/2015/03/nvidia-grid-vgpu-vmware-horizon.html" target="_blank">http://blogs.vmware.com/euc/2015/03/nvidia-grid-vgpu-vmware-horizon.html</a>

These profiles can be specified in VMware vCenter in the properties of the VMs.

<a href="{{site.baseurl}}/assets/images/posts/2016-07-29-change-vmware-vgpu-settings-powershell/vmware-vgpu-profiles.png"><img class="alignnone size-medium wp-image-3698" src="{{site.baseurl}}/assets/images/posts/2016-07-29-change-vmware-vgpu-settings-powershell/vmware-vgpu-profiles.png" alt="vmware-vgpu-profiles" width="287" height="300" /></a>

When you have to change this setting for 16 or 32 VM's it can be time-consuming.
<h2>Changing vGPU settings with PowerShell</h2>
The following script can changes this profile based on the available on the server.

<strong>Please note:</strong> Changing profiles may cause problems so use the script at your own risk.
<pre class="lang:ps decode:true">Param (
        [Parameter(Mandatory=$true)] $hostname,
		[Parameter(Mandatory=$true)] $username,
		[Parameter(Mandatory=$true)] $password,
		[Parameter(Mandatory=$true)] $vmname
    )
	
# Load VMware snapin
Add-PSSnapin VMware*

# Connect to the VMware Host
Connect-VIServer -Server $hostname -User $username -Password $password | Out-Null

# Collect host information
$vmHost = Get-VMHost $hostname

# Define vGPU Profiles
$vGpuProfiles = $vmhost.ExtensionData.Config.SharedPassthruGpuTypes

Write-Host "#############################"
Write-Host "# Available vGPU profiles   #"
Write-Host "# Select your vGPU profiles #"
Write-Host "#############################"

$i = 0
foreach ($vGpuProfile in $vGpuProfiles)
{
	Write-Host "[$i] - $vGpuProfile"
	$i++
}
Write-Host "#############################"

do 
{
	try 
	{	
		$validated = $true
		$max = $i -1 
		[int]$vGpuSelectionInt = Read-Host -Prompt "Please choose a vGPU profile (select between 0 - $max)"
	}
	catch {$validated = $false}
}
until (($vGpuSelectionInt -ge 0 -and $vGpuSelectionInt -le $max) -and $validated)
$vGpuSelection = $vGpuProfiles[$vGpuSelectionInt]
Write-Host "You have selected:" $vGpuSelection

# Collect the VM's that need to change the vGPU profile
$vms = Get-VM -Name $vmName
$vmsOn = $vms | Where-Object {$_.PowerState -eq "PoweredOn"}

Write-Host "VM's wihtin the selection:" $vms.Count
Write-Host "Couple VM's are still powered on, shutdown command will be executed for:" $vmsOn.Count

# Go into the shutdown loop when VM's are still on
if ($vmsOn.Count -gt 0)
{
	do
	{
		# Collect the VM's that need to change the vGPU profile
		$vmsOn = Get-VM -Name $vmName | Where-Object {$_.PowerState -eq "PoweredOn"}
		
		$activeShutdown = $false
		# Shutdown PoweredOn VM's 
		foreach ($vmOn in $vmsOn)
		{
				Write-Host "Shutdown VM:" $vmOn
				if ($vmOn.ExtensionData.Guest.GuestState -eq "running")
				{
					$vmOn | Stop-VMGuest -Confirm:$false | Out-Null
				}
				else
				{
					$vmOn | Stop-VM -Confirm:$false | Out-Null
				}
				
				$activeShutdown = $true
		}
		
		# Sleep when shutdown is active
		if ($activeShutdown)
		{
			Start-Sleep -Seconds 10
		}
	}
	until ($vmsOn.Count -le 0)
}

foreach ($vm in $vms)
{

    $vGPUDevices = $vm.ExtensionData.Config.hardware.Device | Where { $_.backing.vgpu}
    	if ($vGPUDevices.Count -gt 0)
	{
		Write-Host "Remove existing vGPU configuration from VM:" $vm.Name
		foreach ($vGPUDevice in $vGPUDevices)
		{
			$controllerKey = $vGPUDevice.controllerKey
			$key = $vGPUDevice.Key
			$unitNumber = $vGPUDevice.UnitNumber
			$device = $vGPUDevice.device
			$summary = $vGPUDevice.Summary
		  
			$spec = New-Object VMware.Vim.VirtualMachineConfigSpec
			$spec.deviceChange = New-Object VMware.Vim.VirtualDeviceConfigSpec[] (1)
			$spec.deviceChange[0] = New-Object VMware.Vim.VirtualDeviceConfigSpec
			$spec.deviceChange[0].operation = 'remove'
			$spec.deviceChange[0].device = New-Object VMware.Vim.VirtualPCIPassthrough
			$spec.deviceChange[0].device.controllerKey = $controllerKey
			$spec.deviceChange[0].device.unitNumber = $unitNumber
			$spec.deviceChange[0].device.deviceInfo = New-Object VMware.Vim.Description
			$spec.deviceChange[0].device.deviceInfo.summary = $summary
			$spec.deviceChange[0].device.deviceInfo.label = $device
			$spec.deviceChange[0].device.key = $key
			$_this = $VM  | Get-View
			$nulloutput = $_this.ReconfigVM_Task($spec)
		}
	}

	Write-Host "Adding new vGPU configuration from VM:" $vm.Name
	$vmSpec = New-Object VMware.Vim.VirtualMachineConfigSpec
    $vmSpec.deviceChange = New-Object VMware.Vim.VirtualDeviceConfigSpec[] (1)
    $vmSpec.deviceChange[0] = New-Object VMware.Vim.VirtualDeviceConfigSpec
    $vmSpec.deviceChange[0].operation = 'add'
    $vmSpec.deviceChange[0].device = New-Object VMware.Vim.VirtualPCIPassthrough
    $vmSpec.deviceChange[0].device.deviceInfo = New-Object VMware.Vim.Description
    $vmSpec.deviceChange[0].device.deviceInfo.summary = ''
    $vmSpec.deviceChange[0].device.deviceInfo.label = 'New PCI device'
    $vmSpec.deviceChange[0].device.backing = New-Object VMware.Vim.VirtualPCIPassthroughVmiopBackingInfo
    $vmSpec.deviceChange[0].device.backing.vgpu = "$vGpuSelection"
	
    $vmobj = $vm | Get-View
    
	$reconfig = $vmobj.ReconfigVM_Task($vmSpec)
    if ($reconfig) {
        $changedVm = Get-VM $vm
        $vGPUDevice = $changedVm.ExtensionData.Config.hardware.Device | Where { $_.backing.vgpu}
       }   
}
Write-Host "Done with configuration"
timeout /t 15</pre>
<h2>Conclusion</h2>
This script can help you with quickly change the vGPU profiles within VMware so you can continue with testing these settings. I hope this is valuable for you to use and feel free to leave a comment or question.
