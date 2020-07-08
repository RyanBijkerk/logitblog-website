---
layout: post
title: Detect App-V publishing timed out
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Powershell]
image: assets/images/posts/2015-03-23-detect-app-v-publishing-timed-out/detect-app-v-publishing-timed-out-feature-image.png
---
During the European App-V User Group 2015 I presented a session about a new product called <a href="http://www.loginvsi.com/products/login-pi" target="_blank">Login PI</a>. This session was all about how to use Login PI to monitor the user experience and availability of App-V delivered applications. This presentation will be published in the upcoming weeks. In the product demo I used a script to verify if publishing has timed out. Within this blog post I will share how to check if the App-V publishing has timed out with powershell.

The publishing stop Event has ID 19002 and is located in "Applications and Services Logs\Microsoft\App-V\Client\Operational". The 19002 event contains a messages with a return code as you can see in the following picture.

<a href="{{site.baseurl}}/assets/images/posts/2015-03-23-detect-app-v-publishing-timed-out/event-id-19002.png"><img class="alignnone size-medium wp-image-3132" src="{{site.baseurl}}/assets/images/posts/2015-03-23-detect-app-v-publishing-timed-out/event-id-19002.png" alt="event-id-19002" width="300" height="207" /></a>

The first step is to collect the event by powershell. <strong>Please note</strong>: In this example only the first result is selected.
<pre class="lang:ps decode:true">$endEvent = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19002"} | Select -First 1</pre>
By running the above command the event is stored in the $endEvent variable. The next step is to collect the return code from the event message. By using the regex command we can return the line which contains the return code. We can filter the value so we have the exit code.
<pre class="lang:ps decode:true">$endEventMessage = $endEvent.Message
$endEventCode = [regex]::match($endEventMessage, "(.*)Return.*$", "Multiline")

$endEventCharPos = $endEventCode.Value.IndexOf("-")
$endEventCode = $endEventCode.Value.Substring($endEventCharPos +1) -replace " ", ""</pre>
The $endEventCode can be used to check if the publishing has timed out as shown in the following if statement.
<pre class="lang:ps decode:true">if ($endEventCode -eq "0x80072EE2")
{
	Write-host "The operation timed out"
} elseif ($endEventCode -eq "0x0")
{
	Write-Host "App-V Publishing is done"
} else
{
	Write-Host "Unknown exit code: $endTimeCode"
}</pre>
This way you can notify an user something went wrong during publishing and the applications are not delivered. If you have questions or a comment please leave them below.
<h2>Complete code:</h2>
<pre class="lang:ps decode:true">$endEvent = Get-WinEvent -LogName "Microsoft-AppV-Client/Operational" | Where-Object {$_.ID -eq "19002"} | Select -First 1

$endEventMessage = $endEvent.Message
$endEventCode = [regex]::match($endEventMessage, "(.*)Return.*$", "Multiline")

$endEventCharPos = $endEventCode.Value.IndexOf("-")
$endEventCode = $endEventCode.Value.Substring($endEventCharPos +1) -replace " ", ""

if ($endEventCode -eq "0x80072EE2")
{
	Write-host "The operation timed out"
} elseif ($endEventCode -eq "0x0")
{
	Write-Host "App-V Publishing is done"
} else
{
	Write-Host "Unknown exit code: $endTimeCode"
}
</pre>
