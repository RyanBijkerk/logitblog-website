---
layout: post
title: Get ESXtop data automatically from VMa or ESX host
authors: [ryan]
comments: true
categories: [PowerShell]
tags: [ESX, ESXtop, Login VSI, Powershell, VMa, VMware, WINSCP]
image: assets/images/posts/2013-08-20-get-esxtop-data-automatically-from-vma-or-esx-host/get-esxtop-data-automatically-from-vma-or-esx-host-feature-image.png
---
When benchmarking with Login VSI you often want to collect more performance data from your ESX host with ESXtop. I use a script that will start the Login VSI tests automatically and also start capturing ESXtop data. Normally after a test I need to connect to the VMa and download all the ESXtop data of the Login VSI tests and copy the ESXtop files to the correct test directory on the VSIshare. A lot of manual actions so I have automated this. In this blogpost I'm going to share how you can get the ESXtop data from your VMa or ESX host automatically.
I use the tool called <a href="http://winscp.net" target="_blank">WINSCP </a>to connect to the VMa where I can download the ESXtop data. More information about the WINSCP commands can be found <a href="http://winscp.net/eng/docs/commandline" target="_blank">here</a>.

The powershell script is based on the same name for the ESXtop file and the Login VSI test.

Of course we need some information to get this working. At first we need the test name of the Login VSI test and the VSIshare where the results of the test are stored. For WINSCP we need a saved connection and the location of the WINSCP executable. Please note the password is saved in WINSCP. More information about the connection command line can be found <a href="http://winscp.net/eng/docs/commandline#session" target="_blank">here.</a>
<pre class="lang:ps decode:true brush: powershell; gutter: false ">$Testname = "LoginVSI_Win7_2vCPU_Medium_Base_Test"
$VSIshare = "\\VSIserver\VSI-4"
$WinConnection = "vi-admin@vma1.VSI.lab"
$WINSCP = $VSIshare + "\_VSI_Tools\winscp425.exe"

$Temp = $env:temp + "\"
$CSVFile = $Testname + ".csv"

$Command = "/console /command ""option batch abort"" ""open $WinConnection"" ""get $CSVFile $Temp"" ""exit"" "
Start-Process -FilePath $WINSCP -ArgumentList $Command -wait

$File = $Temp + $CSVFile
Copy-Item -Path $File -Destination "$VSIShare\_VSI_Logfiles\$Item\"
Remove-Item -Path $File -Recurse</pre>
By automating this you will save time and the data is stored at one location.

If you have any questions or suggestions just leave a comment.
