---
layout: post
title: Measure App-V publishing time with Login PI
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, Login PI, Powershell]
image: assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/measure-app-v-publishing-time-with-login-pi-feature-image.png
---
I have written a blog post on the Login VSI website about how to measure Microsoft App-V publishing time with Login PI which can be found <a href="http://www.loginvsi.com/blog/370-measure-microsoft-app-v-publishing-time-with-login-pi" target="_blank">here.</a> This blog post contains a detailed description how to integrate App-V publishing in a Login PI workload by using a PowerShell script.

This blog post is originally posted <a href="http://www.loginvsi.com/blog/370-measure-microsoft-app-v-publishing-time-with-login-pi" target="_blank">here</a>.

Microsoft App-V is a great way to virtualize applications and publish them to the desktop. But App-V publishing can take some time and the application is not always available immediately. This can interfere with a Login PI workload. If the workload attempts to launch applications that haven’t been published to the desktop yet, this will result in application start timeouts reported by Login PI. In this blog post I will describe how to integrate App-V publishing in a Login PI workload.

By combining the script in this blog post with your Login PI workload, you will also be able to measure the App-V publishing time, receive reports about this in the Login PI web interface, and generate alerts and notifications when App-V publishing is not performing as expected.
<h2>Script for App-V publishing</h2>
With a PowerShell script we can make sure that the workload will wait until App-V publishing is done. By using Windows Forms within the PowerShell script we can detect this in the Login PI workload. In the end, Login PI will report how long it takes for publishing to take place from a user perspective.

The first step is to load the assembly for Windows Forms. This will be used for creating a form that will be detected by the workload.
<pre class="lang:ps decode:true"># Load assembly
[System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null</pre>
Now we will need to store the current date and time within a variable to make sure that we collect the last start publishing event.
<pre class="lang:ps decode:true"># Get current date / time
$currentTime = Get-Date</pre>
Next, we can create a loop that monitors the Windows Event Log looking for the last start publishing event with ID 19001.
<pre class="lang:ps decode:true"># Collect App-V publishing startTime
Do
{
	$startTime = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19001"} | Select -First 1
} While ($startTime.TimeCreated -gt $currentTime)
Write-Host "Start time:" $startTime.TimeCreated</pre>
This event indicates that publishing has started. Next we will have to wait until publishing is done. This is also done by using a while loop. When the publishing event with ID 19002 is found which contains a more recent date and time stamp as the start event, we can break out of the loop.
<pre class="lang:ps decode:true"># Break variable for loop
$break = $False

# Do loop until publishing is done
Do
{	
	# Collect end of App-V publishing
	$endTime = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19002"} | Select -First 1
	
	#Feedback
	Write-Host "End time:" $endTime.TimeCreated
	
	# If endTime is later then startTime break from loop
	If ($endTime.TimeCreated -gt $startTime.TimeCreated)
	{
		$break = $True
	} 
	else
	{
		Start-Sleep -Milliseconds 500
	}
} While ($break -eq $False)</pre>
To determine if publishing was successful we can use the same method that I recently published on my personal blog: <a href="http://www.logitblog.com/detect-app-v-publishing-timed-out/" target="_blank">Detect App-V publishing timed out</a>.
<pre class="lang:ps decode:true"># Collect end code to check if publishing has a timed out
$endTimeMessage = $endTime.Message
$endTimeCode = [regex]::match($endTimeMessage, "(.*)Return.*$", "Multiline")

$endTimeCharPos = $endTimeCode.Value.IndexOf("-")
$endTimeCode = $endTimeCode.Value.Substring($endTimeCharPos +1) -replace " ", ""
</pre>
Based on the exit code a pop-up will appear so this can be detected by the Login PI workload.
<pre class="lang:ps decode:true">if ($endTimeCode -eq "0x80072EE2")
{
	#Show popup for app_start of Login VSI or Login PI
	[System.Windows.Forms.Messagebox]::Show("The operation timed out", "Timeout")
} else
{
	#Show popup for app_start of Login VSI or Login PI
	[System.Windows.Forms.Messagebox]::Show("App-V Publishing is done", "App-V")
}</pre>
This script can be stored on the Login PI share. I created a directory named scripting on my share where I stored the script which is accessible for all Login PI users.
<h2>Workload integration</h2>
Login PI uses a text-based workload language which can be edited. This workload contains a prepare section which contains basic actions that needs to be done, like adding registry settings. Within the prepare phase we can add the script that we created in the previous step.

Open the workload file and add the following line on top of the prepare phase.
<pre class="lang:default decode:true"># Start App-V script for publishing time
App_Start("AppV", "", "Powershell.exe", "Title", "App-V", "", "-File %VSI_Share%\scripting\AppV.ps1")
App_Close("AppV", "Title", "App-V")
</pre>
Configure the created workload within the workload settings and start the job. The workload engine will execute the script which is visible within the session.
<h2><img class="alignnone size-medium wp-image-3156" src="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/Script.png" alt="Script" width="300" height="238" /></h2>
<h2>Results</h2>
The engine will report the App-V publishing time by the workload action name "AppV" within the dashboard and the charts page.

<a href="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/Dashboard.png"><img class="alignnone size-medium wp-image-3155" src="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/Dashboard.png" alt="" width="300" height="164" /></a>

If the App-V publishing server is down for some reason, publishing will not take place and this will be reported by Login PI as a timeout. This timeout is visible within the dashboard and alerts page.

<a href="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/timeout.png"><img class="alignnone size-medium wp-image-3157" src="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/timeout.png" alt="" width="300" height="238" /></a> <a href="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/Dashboard-timeout.png"><img class="alignnone size-medium wp-image-3158" src="{{site.baseurl}}/assets/images/posts/2015-04-23-measure-app-v-publishing-time-with-login-pi/Dashboard-timeout.png" alt="" width="300" height="164" /></a>
<h2>Conclusion</h2>
By using the script in this blog, it’s very easy to integrate App-V publishing time with Login PI. Now you will be able to receive reports about App-V publishing time in the Login PI web interface, and generate alerts and notifications when App-V publishing is not performing as expected. If you have any further questions or need any help to configure this, please feel free to contact us via the <a href="http://www.loginvsi.com/forum" target="_blank">forums</a>, or at <a href="mailto:support@loginvsi.com" target="_blank">support@loginvsi.com</a>.
<h2>Complete code</h2>
<pre class="lang:ps decode:true"># Load assembly
[System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null

# Get current date / time
$currentTime = Get-Date

# Collect App-V publishing startTime
Do
{
	$startTime = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19001"} | Select -First 1
} While ($startTime.TimeCreated -gt $currentTime)
Write-Host "Start time:" $startTime.TimeCreated

# Break variable for loop
$break = $False

# Do loop until publishing is done
Do
{	
	# Collect end of App-V publishing
	$endTime = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19002"} | Select -First 1
	
	#Feedback
	Write-Host "End time:" $endTime.TimeCreated
	
	# If endTime is later then startTime break from loop
	If ($endTime.TimeCreated -gt $startTime.TimeCreated)
	{
		$break = $True
	} 
	else
	{
		Start-Sleep -Milliseconds 500
	}
} While ($break -eq $False)

# Collect end code to check if publishing has a timed out
$endTimeMessage = $endTime.Message
$endTimeCode = [regex]::match($endTimeMessage, "(.*)Return.*$", "Multiline")

$endTimeCharPos = $endTimeCode.Value.IndexOf("-")
$endTimeCode = $endTimeCode.Value.Substring($endTimeCharPos +1) -replace " ", ""

if ($endTimeCode -eq "0x80072EE2")
{
	#Show popup for app_start of Login PI
	[System.Windows.Forms.Messagebox]::Show("The operation timed out", "Timeout")
} else
{
	#Show popup for app_start of Login PI
	[System.Windows.Forms.Messagebox]::Show("App-V Publishing is done", "App-V")
}
</pre>
<strong>Source</strong>: <a href="http://www.loginvsi.com/blog/370-measure-microsoft-app-v-publishing-time-with-login-pi" target="_blank">http://www.loginvsi.com/blog/370-measure-microsoft-app-v-publishing-time-with-login-pi</a>
<h2>Update</h2>
During the App-V User Group 2015 I presented this content and the recording has been published.

<iframe width="800" height="461" src="https://www.youtube.com/embed/RhZgJOfZHtQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
