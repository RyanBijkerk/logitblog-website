---
layout: post
title: Disable not connected NIC's
authors: [ryan]
comments: true
categories: [PowerShell]
tags: [NIC, Powershell, Scripting]
image: assets/images/posts/2012-02-23-disable-not-connected-nics/disable-not-connected-nics-feature-image.png
---
My colleague <a href="http://www.geursen.net/" target="_blank">Niels Geursen</a> called me and asked if I could help him with some scripting. At his customer, monitoring software generates a pop-up window with a warning about some NIC's that were not connected. By disabling the NIC's the error does not appear. To avoid manual actions on all Citrix servers he wanted to created a script to collect all disconnected NIC's and disable them. To help Niels I wrote the following script.
<pre class="lang:ps decode:true brush: powershell; gutter: false "># Collect Disconnected NIC's

$NIC = Get-WmiObject -Namespace "root\cimv2" -Query "select * from `
Win32_NetworkAdapter WHERE PhysicalAdapter = 'True'`
and NetConnectionStatus = '7'"

IF ($NIC -like "") {Exit}
# Show Results is optional.

$NIC | Select-Object NetConnectionID, Name

# Disable NIC's

$objNIC = $NIC.Count

IF ($objNIC -like "") {
$NIC.disable() | Out-Null
} Else {

$i = 0

DO {
$disNIC = $NIC[$i] ; $i++

$disNIC.Disable() | Out-Null
} While ($i -le $objNIC -1 )
}</pre>