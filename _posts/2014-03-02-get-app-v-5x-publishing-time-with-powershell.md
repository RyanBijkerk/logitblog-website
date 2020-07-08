---
layout: post
title: Get App-V 5.x Publishing Time with PowerShell
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Event viewer, Powershell]
image: assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/get-app-v-5x-publishing-time-with-powershell-feature-image.png
---
When publishing App-V 5.x applications it is interesting to know how long it takes to publish all applications. I needed this information during a large scale test to get a better understanding of the impact on the user experience. I also wanted to know if the publishing time increases when more users are active. This information can be used to optimize the publishing time and eventually the user experience.

In this blog post I will share a powershell function that can get the App-V publishing time on a remote specific machine.
<h2>Where do we get the data from?</h2>
With App-V 5.x the publishing events are logged in the event viewer. Event 19001 is the event which will indicate publishing has started. The time of this event is our starting point.

<a href="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Start-AppV-Publishing-Event.png"><img class="size-medium wp-image-2753 alignnone" src="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Start-AppV-Publishing-Event.png" alt="Start-AppV-Publishing-Event" width="300" height="123" /></a>

Event 19002 is the closing event which indicates the publishing is finished.
<h2><a href="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Stop-AppV-Publishing-Event.png"><img class="alignnone size-medium wp-image-2754" src="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Stop-AppV-Publishing-Event.png" alt="Stop-AppV-Publishing-Event" width="300" height="180" /></a></h2>
Based on the time between the two events we can calculate the time during these events. This will result in the total publishing time of App-V.
<h2>Powershell function</h2>
With the following powershell function you can get the publishing time on a remote computer. The function will always get the latest publishing time.
<pre class="lang:ps decode:true brush: powershell; gutter: false ">Function Get-AppVPublishingTime($ClientName)
{
	# Sets the return hashtable
	[hashtable]$Return = @{}

	# Get the used event-logs of the target machine
	$EventLogs = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" -ComputerName "$ClientName" | Where-Object {$_.ID -eq "19001" -or $_.ID -eq "19002"}

	# Get the latest publishing time
	Foreach ($Item in ($EventLogs | Where-Object {$_.ID -eq 19001}))
	{
		If ($Time -le $Item.TimeCreated)
		{$LastPublishTime = $Item}
		$Time = $Item.TimeCreated
	}

	# get the latest publishing finished time
	Foreach ($Item in ($EventLogs | Where-Object {$_.ID -eq 19002}))
	{
		If ($Time -le $Item.TimeCreated)
		{$LastPublishDoneTime = $Item}
		$Time = $Item.TimeCreated
	}

	# If the last publish time is higher than the done time no result will be return
	If ($LastPublishTime.TimeCreated -le $LastPublishDoneTime.TimeCreated)
	{
		$PublishTime = $LastPublishDoneTime.TimeCreated - $LastPublishTime.TimeCreated
	}

	# Add entries to return hashtable
	$Return.LastPublishTime = $LastPublishTime.TimeCreated
	$Return.ComputerName = $ClientName
	$Return.PublishTime = $PublishTime.TotalSeconds

	# Return data
	Return $Return
}</pre>
The text based version of the powershell function can be found <a href="http://www.logitblog.com/downloads/Get-AppVPublishingTime.txt" target="_blank">here</a>.
Run the command as followed:
<pre class="lang:ps decode:true brush: powershell; gutter: false ">$PublishedData = Get-AppVPublishingTime W7-x86-AppV</pre>
The variable "PublishedData" contains the following information:

<a href="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Powershell-PublishedData.png"><img class="alignnone size-full wp-image-2752" src="{{site.baseurl}}/assets/images/posts/2014-03-02-get-app-v-5x-publishing-time-with-powershell/Powershell-PublishedData.png" alt="Powershell-PublishedData" width="514" height="170" /></a>

$PublishedData.ComputerName - The computer name that is used as the parameter
$PublishedData.PublishTime - The duration of the publishing the App-V applications in seconds
$PublishedData.LastPublishTime - The date &amp; time of the last publishing action

If the variable $PublishedData.PublishTime does not contain a value the publishing is probably not finished.

The powershell function can provide valuable information to get an overview of the App-V 5.x publishing time in your environment. If you have any questions or comments please leave a comment below.