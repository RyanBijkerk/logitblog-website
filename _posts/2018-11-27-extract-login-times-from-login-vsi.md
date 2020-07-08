---
layout: post
title: Extract login times from Login VSI
authors: [ryan]
comments: true
categories: [Login VSI]
tags: [Citrix, Login VSI, Login VSI, Microsoft, Powershell, VMware]
image: assets/images/posts/2018-11-27-extract-login-times-from-login-vsi/extract-login-times-from-login-vsi-feature-image.png
---
The login time is a key metric that has a big effect on the user experience. The login times are often included in service-level agreements to ensure a good user experience. When applying changes in your environment you want to avoid increasing login times. With Login VSI you can extract the login times from the data. This blog post will show how to extract the login times from Login VSI into raw CSV data.

<!--more-->
<blockquote><strong>Please note</strong>: this blog post expects some basic knowledge of the product Login VSI. More information about Login VSI can be found <a href="https://www.loginvsi.com/products/login-vsi" target="_blank" rel="noopener">here</a>.</blockquote>
<h2>Login process from a Login VSI perspective</h2>
It's important to understand the login process from a Login VSI perspective. The Login VSI login process contains three stages:
<ol>
 	<li>Launched;</li>
 	<li>Logon;</li>
 	<li>Active.</li>
</ol>
<h3>Launched</h3>
Once the test is started the Launcher will establish a user session. Before starting the configured connector, a file will be written on the share on the following location: <em>"[VSIShare]\_VSI_LogFiles\[ActiveTest]\Monitor\LaunchedSessions\". </em>In this location, text files are created that contain basic information, including the launcher name and username.
<h3>Logon</h3>
With a default deployment of Login VSI a login script is used to start the engine in the desktop. Once the engine is started in the login mode another file is created on the share: <em>"[VSIShare]\_VSI_LogFiles\[ActiveTest]\Monitor\Logon\". </em>This text file contains the username and desktop name.
<h3>Active</h3>
By default, the engine will exit the logon mode and restarts in active mode. Active mode is where the engine will execute the workload. Again another file is created but this time in the following folder:  <em>"[VSIShare]\_VSI_LogFiles\[ActiveTest]\Monitor\AcitveSessions\". </em>These text files also contain the username and desktop name.

The time between launched and active is the total login time which includes brokering, group policy processing, user profile loading etc. Based on these files the total login time can be extracted. Some files contain timestamps. It is recommended to use the last write or creation timestamps of the file server to get accurate results. Time drifting may occur when using timestamps from various machines.
<h2>The script</h2>
Before starting it is important to understand the times are based on the last write time of the file. Moving the files to another location will influence the results.
<pre class="lang:ps decode:true ">$testNameFilter = "Name_of_the_test"
$tests = Get-ChildItem -Path "\\server\VSIShare\_VSI_LogFiles"  | Where-Object {$_.Name.StartsWith($testNameFilter)}


foreach ($test in $tests) {

	$testPath = $test.FullName

	$userLogons = @()
	$files = Get-ChildItem "$testPath\Monitor\LaunchedSessions\*.txt"
	foreach ($file in $files) {
		
		$match = $file.name.Replace(".txt","") -match '(?&lt;=\##)(.*?)(?=_#)'
		if($match) {
			
			$userID = $Matches[0]

			$activeFiles = Get-ChildItem "$testPath\Monitor\ActiveSessions\$userID*.log"

			foreach ($fileActive in $activeFiles) {

				if($fileActive.name.Split('@')[0] -eq $userID) {

					$item = New-Object PSObject    
					$item | Add-Member Noteproperty DateTime $file.LastWriteTime.ToString('MM/dd/yyyy HH:mm:ss')                       
					$item | Add-Member Noteproperty User $userid
					$item | Add-Member Noteproperty LogonTime ($fileActive.LastWriteTime - $file.LastWriteTime).TotalSeconds

					$userLogons += $item
				}	
			}
		}
	}

	$match = $test.Name -Match "(\d+)(?!.*\d)"
	$number = $matches[0]
	$name = $test.Name.TrimEnd($number) + "Logon_" + $number

	$userLogons | Sort-Object DateTime | Export-Csv "$testPath\$($name).csv" -NoTypeInformation
}
</pre>
The login times are based on the launched and the active file. The information is saved within an object including the launched time, username and total login time which is added to an array. At the end of the script, the array is exported to a CSV located in the test directory. The following screenshot shows the results. Please note I have added the chart myself, this is not done by the script.

<a href="{{site.baseurl}}/assets/images/posts/2018-11-27-extract-login-times-from-login-vsi/login_times_loginvsi.png"><img class="alignnone size-medium wp-image-4762" src="{{site.baseurl}}/assets/images/posts/2018-11-27-extract-login-times-from-login-vsi/login_times_loginvsi.png" alt="" width="300" height="243" /></a>
<h2>Conclusion</h2>
The login time is an important metric which is often included in a service-level agreement. It is possible to extract the login times from the Login VSI results using a simple script. This is valuable information to validate if a change to the environment has an impact on the login times. If you have any comments or questions please leave them below.

Credits to <a href="https://www.linkedin.com/in/dennis-geerlings-13411933" target="_blank" rel="noopener">Dennis Geerlings</a> from Login VSI that provided a base for the script.

Photo by <a href="https://unsplash.com/photos/BXOXnQ26B7o?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Aron</a> on <a href="https://unsplash.com/search/photos/time?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a>