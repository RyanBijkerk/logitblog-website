---
layout: post
title: How to capture NVIDIA GRID and Tesla performance data
authors: [ryan]
comments: true
categories: [NVIDIA]
tags: [GRID, Nvidia, NVIDIA, Powershell, Tesla]
image: assets/images/posts/2017-07-16-how-to-capture-nvidia-grid-and-tesla-performance-data/how-to-capture-nvidia-grid-and-tesla-performance-data-feature-image.png
---
When researching new technologies in a virtual desktop scenario it is very important to understand what is going on. Capturing performance data is crucial to do proper analysis and to fully understand the impact of your scenario. In this blog post, I'm going to share how to capture the performance data from NVIDIA GRID or Telsa cards.

<!--more-->
<h2>The nvidia-smi command line</h2>
The NVIDIA drivers on the hypervisor come with a tool named "nvidia-smi" which can be used for multiple purposes including capturing performance data. To capture the data in a file the following command line is used:
<pre class="lang:sh decode:true">nvidia-smi -l [interval] -f [path] --format=[format] --query-gpu=[query]</pre>
The -l parameter is the interval in seconds for capturing the data and the -f parameter you can specify the path to store the results. The --format parameter can be used for the following options.
<ul>
 	<li>csv - comma separated values (mandatory)</li>
 	<li>noheader - skips the first line with column headers</li>
 	<li>nounits - don't print units for numerical values</li>
</ul>
The --query-gpu parameter is where the specific queries are specified that needs to be captured. For the complete overview of all available queries please see the NVIDIA query overview section further down in this post.

The following command line is a working example.
<pre class="lang:sh decode:true">nvidia-smi -l 30 -f /root/Example_test.csv --format=csv,nouints --query-gpu=timestamp,utilization.gpu,utilization.memory,memory.total,memory.used,memory.free</pre>
For additional information about nvidia-smi please check this <a href="http://nvidia.custhelp.com/app/answers/detail/a_id/3751/~/useful-nvidia-smi-queries" target="_blank" rel="noopener">page</a>.
<h2>Automate performance capture with PowerShell</h2>
When doing repeating performance tests capturing this data is very important. Of course, you want to automate this so you don't have to do this manually every time. By using the PowerShell module Posh-SSH you are able to invoke a command to the hypervisor. To install the Posh-SSH module run: "Install-Module -Name Posh-SSH".

In the following example I'm using a job to capture the data so the existing PowerShell instance is not stuck during the data capture.
<pre class="lang:ps decode:true">Import-Module -Name Posh-SSH

$hostName = "XenServer01"
$testName= "Example_test"
$password = ConvertTo-SecureString "password" -AsPlainText -Force
$hostCredential = New-Object System.Management.Automation.PSCredential ("root", $password)

$delay = 30
$timeout = 3600

$path = "/root/$testName.csv"
$command = "nvidia-smi -l $delay -f $path --format=csv,nounits --query-gpu=timestamp,utilization.gpu,utilization.memory,memory.total,memory.used,memory.free"

Start-Job -Scriptblock {
     Param(
          [PSCredential]$jobCred,
          [string]$jobHost,
          [string]$jobCommand,
          [int]$jobTimeout
     )

     $session = New-SSHSession -ComputerName $jobHost -Credential $jobCred -AcceptKey
     Invoke-SSHCommand -Index $session.SessionId -Command $jobCommand -TimeOut $jobTimeout
     Get-SSHSession | Remove-SSHSession | Out-Null
} -ArgumentList @($hostCredential, $HostName, $command, $timeout)
</pre>
<h2>Analyzing the results</h2>
Once the data is captured you can download the csv file from the hypervisor using PowerShell or a tool like WinSCP. It is important to understand the data is captured per physical card and per core of the card. The following example data is captured on two Telsa M60 cards which mean there are 4 lines per measurement.

<a href="{{site.baseurl}}/assets/images/posts/2017-07-16-how-to-capture-nvidia-grid-and-tesla-performance-data/excel_example_test_overview.png"><img class="alignnone size-medium wp-image-4354" src="{{site.baseurl}}/assets/images/posts/2017-07-16-how-to-capture-nvidia-grid-and-tesla-performance-data/excel_example_test_overview.png" alt="" width="244" height="300" /></a>

Creating a chart of the default output is not really readable. To improve this you can group the data of one measurement in an average number. The following Excel formula will create an average per blocks of 4.

=AVERAGE(OFFSET(Example_test!$B$2,(ROW()-ROW($B$2))*4,,4,))
<blockquote><strong>Please note:</strong> make sure to select the correct sheet and cell.</blockquote>
<a href="{{site.baseurl}}/assets/images/posts/2017-07-16-how-to-capture-nvidia-grid-and-tesla-performance-data/excel_example_test_grouped.png"><img class="alignnone size-medium wp-image-4355" src="{{site.baseurl}}/assets/images/posts/2017-07-16-how-to-capture-nvidia-grid-and-tesla-performance-data/excel_example_test_grouped.png" alt="" width="300" height="158" /></a>
<h2>Conclusion</h2>
When researching performance data helps to understand the impact of your scenario. It's quite easy to capture the NVIDIA data using the nividia-smi tool. Using the PowerShell Posh-SSH allows you to capture the performance data automatically. With Excel, the data of each physical and core can be grouped to create nice readable charts. For a complete overview of all NVIDIA queries please see the table specified below. I hope this post helps you in achieving your goal and if you have comments or questions please leave them below!
<h2>NVIDIA query overview</h2>
The following table shows all available queries provided by NVIDIA.
<blockquote><strong>Please note: </strong>The following table may be is captured with the nvidia-smi command line version v367.106, a different version may have additional metrics. To get the complete overview run: "nvidia-smi --help-query-gpu"</blockquote>
<table>
<tbody>
<tr>
<td width="150"><strong>Query</strong></td>
<td style="text-align: left;"><strong>Description</strong></td>
</tr>
<tr>
<td width="150">timestamp</td>
<td style="text-align: left;">The timestamp of where the query was made in format "YYYY/MM/DD HH:MM:SS.msec".</td>
</tr>
<tr>
<td width="150">driver_version</td>
<td style="text-align: left;">The version of the installed NVIDIA display driver. This is an alphanumeric string.</td>
</tr>
<tr>
<td width="150">count</td>
<td style="text-align: left;">The number of NVIDIA GPUs in the system.</td>
</tr>
<tr>
<td width="150">name or gpu_name</td>
<td style="text-align: left;">The official product name of the GPU. This is an alphanumeric string. For all products.</td>
</tr>
<tr>
<td width="150">serial or gpu_serial</td>
<td style="text-align: left;">This number matches the serial number physically printed on each board. It is a globally unique immutable alphanumeric value.</td>
</tr>
<tr>
<td width="150">pci.bus_id</td>
<td style="text-align: left;">PCI bus id as "domain:bus:device.function", in hex.</td>
</tr>
<tr>
<td width="150">uuid or gpu_uuid</td>
<td style="text-align: left;">This value is the globally unique immutable alphanumeric identifier of the GPU. It does not correspond to any physical label on the board.</td>
</tr>
<tr>
<td width="150">pci.bus_id or gpu_bus_id</td>
<td style="text-align: left;">PCI bus id as "domain:bus:device.function", in hex.</td>
</tr>
<tr>
<td width="150">pci.domain</td>
<td style="text-align: left;">PCI domain number, in hex.</td>
</tr>
<tr>
<td width="150">pci.bus</td>
<td style="text-align: left;">PCI bus number, in hex.</td>
</tr>
<tr>
<td width="150">pci.device</td>
<td style="text-align: left;">PCI device number, in hex.</td>
</tr>
<tr>
<td width="150">pci.device_id</td>
<td style="text-align: left;">PCI vendor device id, in hex.</td>
</tr>
<tr>
<td width="150">pci.sub_device_id</td>
<td style="text-align: left;">PCI Sub System id, in hex.</td>
</tr>
<tr>
<td width="150">pcie.link.gen.current</td>
<td style="text-align: left;">The current PCI-E link generation. These may be reduced when the GPU is not in use.</td>
</tr>
<tr>
<td width="150">pcie.link.gen.max</td>
<td style="text-align: left;">The maximum PCI-E link generation possible with this GPU and system configuration. For example, if the GPU supports a higher PCIe generation than the system supports then this reports the system PCIe generation.</td>
</tr>
<tr>
<td width="150">pcie.link.width.current</td>
<td style="text-align: left;">The current PCI-E link width. These may be reduced when the GPU is not in use.</td>
</tr>
<tr>
<td width="150">pcie.link.width.max</td>
<td style="text-align: left;">The maximum PCI-E link width possible with this GPU and system configuration. For example, if the GPU supports a higher PCIe generation than the system supports then this reports the system PCIe generation.</td>
</tr>
<tr>
<td width="150">index</td>
<td style="text-align: left;">Zero based index of the GPU. Can change at each boot.</td>
</tr>
<tr>
<td width="150">display_mode</td>
<td style="text-align: left;">A flag that indicates whether a physical display (e.g. monitor) is currently connected to any of the GPU's connectors. "Enabled" indicates an attached display. "Disabled" indicates otherwise.</td>
</tr>
<tr>
<td width="150">display_active</td>
<td style="text-align: left;">A flag that indicates whether a display is initialized on the GPU's (e.g. memory is allocated on the device for display). Display can be active even when no monitor is physically attached. "Enabled" indicates an active display. "Disabled" indicates otherwise.</td>
</tr>
<tr>
<td width="150">persistence_mode</td>
<td style="text-align: left;">A flag that indicates whether persistence mode is enabled for the GPU. Value is either "Enabled" or "Disabled". When persistence mode is enabled the NVIDIA driver remains loaded even when no active clients, such as X11 or nvidia-smi, exist. This minimizes the driver load latency associated with running dependent apps, such as CUDA programs. Linux only.</td>
</tr>
<tr>
<td width="150">accounting.mode</td>
<td style="text-align: left;">A flag that indicates whether accounting mode is enabled for the GPU. Value is either "Enabled" or "Disabled". When accounting is enabled statistics are calculated for each compute process running on the GPU.Statistics can be queried during the lifetime or after termination of the process.The execution time of the process is reported as 0 while the process is in running state and updated to actual execution time after the process has terminated. See --help-query-accounted-apps for more info.</td>
</tr>
<tr>
<td width="150">accounting.buffer_size</td>
<td style="text-align: left;">The size of the circular buffer that holds list of processes that can be queried for accounting stats. This is the maximum number of processes that accounting information will be stored for before information about oldest processes will get overwritten by information about new processes.</td>
</tr>
<tr>
<td width="150">driver_model.current</td>
<td style="text-align: left;">The driver model currently in use. Always "N/A" on Linux.</td>
</tr>
<tr>
<td width="150">driver_model.pending</td>
<td style="text-align: left;">The driver model that will be used on the next reboot. Always "N/A" on Linux.</td>
</tr>
<tr>
<td width="150">vbios_version</td>
<td style="text-align: left;">The BIOS of the GPU board.</td>
</tr>
<tr>
<td width="150">inforom.img or inforom.image</td>
<td style="text-align: left;">Global version of the infoROM image. Image version just like VBIOS version uniquely describes the exact version of the infoROM flashed on the board in contrast to infoROM object version which is only an indicator of supported features.</td>
</tr>
<tr>
<td width="150">inforom.oem</td>
<td style="text-align: left;">Version for the OEM configuration data.</td>
</tr>
<tr>
<td width="150">inforom.ecc</td>
<td style="text-align: left;">Version for the ECC recording data.</td>
</tr>
<tr>
<td width="150">inforom.pwr or inforom.power</td>
<td style="text-align: left;">Version for the power management data.</td>
</tr>
<tr>
<td width="150">gom.current or gpu_operation_mode.current</td>
<td style="text-align: left;">The GOM currently in use.</td>
</tr>
<tr>
<td width="150">gom.pending or gpu_operation_mode.pending</td>
<td style="text-align: left;">The GOM that will be used on the next reboot.</td>
</tr>
<tr>
<td width="150">fan.speed</td>
<td style="text-align: left;">The fan speed value is the percent of maximum speed that the device's fan is currently intended to run at. It ranges from 0 to 100 %. Note: The reported speed is the intended fan speed. If the fan is physically blocked and unable to spin, this output will not match the actual fan speed. Many parts do not report fan speeds because they rely on cooling via fans in the surrounding enclosure.</td>
</tr>
<tr>
<td width="150">pstate</td>
<td style="text-align: left;">The current performance state for the GPU. States range from P0 (maximum performance) to P12 (minimum performance).</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.supported</td>
<td style="text-align: left;">Bitmask of supported clock throttle reasons. See nvml.h for more details.</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.active</td>
<td style="text-align: left;">Bitmask of active clock throttle reasons. See nvml.h for more details.</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.gpu_idle</td>
<td style="text-align: left;">Nothing is running on the GPU and the clocks are dropping to Idle state. This limiter may be removed in a later release.</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.applications_clocks_setting</td>
<td style="text-align: left;">GPU clocks are limited by applications clocks setting. E.g. can be changed by nvidia-smi --applications-clocks=</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.sw_power_cap</td>
<td style="text-align: left;">SW Power Scaling algorithm is reducing the clocks below requested clocks because the GPU is consuming too much power. E.g. SW power cap limit can be changed with nvidia-smi --power-limit=</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.hw_slowdown</td>
<td style="text-align: left;">HW Slowdown (reducing the core clocks by a factor of 2 or more) is engaged. This is an indicator of:
* temperature being too high;
* External Power Brake Assertion is triggered (e.g. by the system power supply);
* Power draw is too high and Fast Trigger protection is reducing the clocks;
* May be also reported during PState or clock change;
* This behavior may be removed in a later release.</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.sync_boost</td>
<td style="text-align: left;">Sync Boost This GPU has been added to a Sync boost group with nvidia-smi or DCGM in
* order to maximize performance per watt. All GPUs in the sync boost group;
* will boost to the minimum possible clocks across the entire group. Look at;
* the throttle reasons for other GPUs in the system to see why those GPUs are;
* holding this one at lower clocks.</td>
</tr>
<tr>
<td width="150">clocks_throttle_reasons.unknown</td>
<td style="text-align: left;">Some other unspecified factor is reducing the clocks.</td>
</tr>
<tr>
<td width="150">memory.total</td>
<td style="text-align: left;">Total installed GPU memory.</td>
</tr>
<tr>
<td width="150">memory.used</td>
<td style="text-align: left;">Total memory allocated by active contexts.</td>
</tr>
<tr>
<td width="150">memory.free</td>
<td style="text-align: left;">Total free memory.</td>
</tr>
<tr>
<td width="150">compute_mode</td>
<td style="text-align: left;">The compute mode flag indicates whether individual or multiple compute applications may run on the GPU.
"Default" means multiple contexts are allowed per device.
"Exclusive_Process" means only one context is allowed per device, usable from multiple threads at a time.
"Prohibited" means no contexts are allowed per device (no compute apps).</td>
</tr>
<tr>
<td width="150">utilization.gpu</td>
<td style="text-align: left;">Percent of time over the past sample period during which one or more kernels was executing on the GPU.
The sample period may be between 1 second and 1/6 second depending on the product.</td>
</tr>
<tr>
<td width="150">utilization.memory</td>
<td style="text-align: left;">Percent of time over the past sample period during which global (device) memory was being read or written.
The sample period may be between 1 second and 1/6 second depending on the product.</td>
</tr>
<tr>
<td width="150">ecc.mode.current</td>
<td style="text-align: left;">The ECC mode that the GPU is currently operating under.</td>
</tr>
<tr>
<td width="150">ecc.mode.pending</td>
<td style="text-align: left;">The ECC mode that the GPU will operate under after the next reboot.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.device_memory</td>
<td style="text-align: left;">Errors detected in global device memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.register_file</td>
<td style="text-align: left;">Errors detected in register file memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.l1_cache</td>
<td style="text-align: left;">Errors detected in the L1 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.l2_cache</td>
<td style="text-align: left;">Errors detected in the L2 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.texture_memory</td>
<td style="text-align: left;">Parity errors detected in texture memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.volatile.total</td>
<td style="text-align: left;">Total errors detected across entire chip. Sum of device_memory, register_file, l1_cache, l2_cache and texture_memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.device_memory</td>
<td style="text-align: left;">Errors detected in global device memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.register_file</td>
<td style="text-align: left;">Errors detected in register file memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.l1_cache</td>
<td style="text-align: left;">Errors detected in the L1 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.l2_cache</td>
<td style="text-align: left;">Errors detected in the L2 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.texture_memory</td>
<td style="text-align: left;">Parity errors detected in texture memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.corrected.aggregate.total</td>
<td style="text-align: left;">Total errors detected across entire chip. Sum of device_memory, register_file, l1_cache, l2_cache and texture_memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.device_memory</td>
<td style="text-align: left;">Errors detected in global device memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.register_file</td>
<td style="text-align: left;">Errors detected in register file memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.l1_cache</td>
<td style="text-align: left;">Errors detected in the L1 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.l2_cache</td>
<td style="text-align: left;">Errors detected in the L2 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.texture_memory</td>
<td style="text-align: left;">Parity errors detected in texture memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.volatile.total</td>
<td style="text-align: left;">Total errors detected across entire chip. Sum of device_memory, register_file, l1_cache, l2_cache and texture_memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.device_memory</td>
<td style="text-align: left;">Errors detected in global device memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.register_file</td>
<td style="text-align: left;">Errors detected in register file memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.l1_cache</td>
<td style="text-align: left;">Errors detected in the L1 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.l2_cache</td>
<td style="text-align: left;">Errors detected in the L2 cache.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.texture_memory</td>
<td style="text-align: left;">Parity errors detected in texture memory.</td>
</tr>
<tr>
<td width="150">ecc.errors.uncorrected.aggregate.total</td>
<td style="text-align: left;">Total errors detected across entire chip. Sum of device_memory, register_file, l1_cache, l2_cache and texture_memory.</td>
</tr>
<tr>
<td width="150">retired_pages.single_bit_ecc.count or retired_pages.sbe</td>
<td style="text-align: left;">The number of GPU device memory pages that have been retired due to multiple single bit ECC errors.</td>
</tr>
<tr>
<td width="150">retired_pages.double_bit.count or retired_pages.dbe</td>
<td style="text-align: left;">The number of GPU device memory pages that have been retired due to a double bit ECC error.</td>
</tr>
<tr>
<td width="150">retired_pages.pending</td>
<td style="text-align: left;">Checks if any GPU device memory pages are pending retirement on the next reboot. Pages that are pending retirement can still be allocated, and may cause further reliability issues.</td>
</tr>
<tr>
<td width="150">temperature.gpu</td>
<td style="text-align: left;">Core GPU temperature. in degrees C.</td>
</tr>
<tr>
<td width="150">power.management</td>
<td style="text-align: left;">A flag that indicates whether power management is enabled. Either "Supported" or "[Not Supported]". Requires Inforom PWR object version 3.0 or higher or Kepler device.</td>
</tr>
<tr>
<td width="150">power.draw</td>
<td style="text-align: left;">The last measured power draw for the entire board, in watts. Only available if power management is supported. This reading is accurate to within +/- 5 watts.</td>
</tr>
<tr>
<td width="150">power.limit</td>
<td style="text-align: left;">The software power limit in watts. Set by software like nvidia-smi. On Kepler devices Power Limit can be adjusted using [-pl | --power-limit=] switches.</td>
</tr>
<tr>
<td width="150">enforced.power.limit</td>
<td style="text-align: left;">The power management algorithm's power ceiling, in watts. Total board power draw is manipulated by the power management algorithm such that it stays under this value. This value is the minimum of various power limiters.</td>
</tr>
<tr>
<td width="150">power.default_limit</td>
<td style="text-align: left;">The default power management algorithm's power ceiling, in watts. Power Limit will be set back to Default Power Limit after driver unload.</td>
</tr>
<tr>
<td width="150">power.min_limit</td>
<td style="text-align: left;">The minimum value in watts that power limit can be set to.</td>
</tr>
<tr>
<td width="150">power.max_limit</td>
<td style="text-align: left;">The maximum value in watts that power limit can be set to.</td>
</tr>
<tr>
<td width="150">clocks.current.graphics or clocks.gr</td>
<td style="text-align: left;">Current frequency of graphics (shader) clock.</td>
</tr>
<tr>
<td width="150">clocks.current.sm or clocks.sm</td>
<td style="text-align: left;">Current frequency of SM (Streaming Multiprocessor) clock.</td>
</tr>
<tr>
<td width="150">clocks.current.memory or clocks.mem</td>
<td style="text-align: left;">Current frequency of memory clock.</td>
</tr>
<tr>
<td width="150">clocks.current.video or clocks.video</td>
<td style="text-align: left;">Current frequency of video encoder/decoder clock.</td>
</tr>
<tr>
<td width="150">clocks.applications.graphics or clocks.applications.gr</td>
<td style="text-align: left;">User specified frequency of graphics (shader) clock.</td>
</tr>
<tr>
<td width="150">clocks.applications.memory or clocks.applications.mem</td>
<td style="text-align: left;">User specified frequency of memory clock.</td>
</tr>
<tr>
<td width="150">clocks.default_applications.graphics or clocks.default_applications.gr</td>
<td style="text-align: left;">Default frequency of applications graphics (shader) clock.</td>
</tr>
<tr>
<td width="150">clocks.default_applications.memory or clocks.default_applications.mem</td>
<td style="text-align: left;">Default frequency of applications memory clock.</td>
</tr>
<tr>
<td width="150">clocks.max.graphics or clocks.max.gr</td>
<td style="text-align: left;">Maximum frequency of graphics (shader) clock.</td>
</tr>
<tr>
<td width="150">clocks.max.sm or clocks.max.sm</td>
<td style="text-align: left;">Maximum frequency of SM (Streaming Multiprocessor) clock.</td>
</tr>
<tr>
<td width="150">clocks.max.memory or clocks.max.mem</td>
<td style="text-align: left;">Maximum frequency of memory clock.</td>
</tr>
</tbody>
</table>


<span style="text-decoration: underline;">Credits feature image:</span> <a href="https://unsplash.com/@kmuza" target="_blank" rel="noopener">Carlos Muza</a>
