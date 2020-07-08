---
layout: post
title: TeamCity Build Music
authors: [ryan]
comments: true
categories: [Development]
tags: [API, Development, Powershell, TeamCity]
image: assets/images/posts/2015-08-30-teamcity-build-music/teamcity-build-music-feature-image.png
---
With development multiple builds are started during the day. I noticed that I'm not sure if a build is still running and I'm forced to look into the web interface or ask a colleague. To get the attention when build is running I have created a PowerShell script that will start music when a build is started in TeamCity. This will notify the entire team a build is running and if the build is successful or has failed. In this blog post I will share how to use the TeamCity API to start music awesome build music.

At first you will need a system that has some speakers and is near the team. In my case we have a dedicated laptop with speakers to do our SCRUM ceremonies so I took advantage of this machine.

Please note, this version is specifically created for TeamCity but the concept should also work with other solutions (if a API is available).
<h2>What is TeamCity?</h2>
TeamCity is a powerful and user-friendly Continuous Integration and Deployment server that works out of the box. More information can be found on the <a href="https://www.jetbrains.com/teamcity/" target="_blank">JetBrains website</a>.
<h2>TeamCity authentication</h2>
TeamCity requires authentication on the API so we have to define an username and password (in this example the password is plain text).
<pre class="lang:ps decode:true"># Define authentication
$pw = convertto-securestring -AsPlainText -Force -String "PasswordInText"
$cred = new-object -typename System.Management.Automation.PSCredential -argumentlist "UserName",$pw</pre>
<h2>TeamCity API Calls</h2>
The following call is to retrieve the running builds within TeamCity. This call is used to verify if a build is running.
<pre class="lang:ps decode:true">	# Base API to retrive running builds
	$url = $teamCityServer + "/httpAuth/app/rest/builds?locator=running:true"
	$apiCall = Invoke-RestMethod -Uri $url -Credential $cred</pre>
The result will be the running builds. This result can be used to collect the status of the running build by using the following command:
<pre class="lang:ps decode:true"># Set results
$builds = $apiCall.builds.build
$runningBuild = $builds

$finishedUrl = $teamCityServer + $runningBuild.href

$finishedApiCall = Invoke-RestMethod -Uri $finishedUrl -Credential $cred
$finishedBuild = $finishedApiCall.build

$finishedBuild.status
</pre>
The status will show if the build is SUCCESS (successful) or FAILURE (failed).
<h2>Complete PowerShell script</h2>
Here you can find the complete PowerShell script I'm using to play some music during the build. Please note the music success.mp3 and failure.mp3 are static but the running build music is dynamic and should be located in the running directory where the script is located.
<pre class="lang:ps decode:true"># Define authentication
$pw = convertto-securestring -AsPlainText -Force -String "PasswordInText"
$cred = new-object -typename System.Management.Automation.PSCredential -argumentlist "UserName",$pw

# Set loop to endless
$endless = $true

# Set current location
$currentPath = split-path -parent $MyInvocation.MyCommand.Definition

# Define music for success and failure
$successSound = $currentPath + "\success.mp3"
$failureSound = $currentPath + "\failure.mp3"

# TeamCity Server
$teamCityServer = "http://teamcity:8080"

# Set bool for finished
$musicFinished = $false

do
{
	# Base API to retrive running builds
	$url = $teamCityServer + "/httpAuth/app/rest/builds?locator=running:true"
	$apiCall = Invoke-RestMethod -Uri $url -Credential $cred

	# Set results
	$builds = $apiCall.builds.build

	# Set music is not running
	$musicRunning = $false
	
		do
		{
			# Check for running build
			if ($builds.state -eq "running")
			{
				# If music is not running
				if (!$musicRunning)
				{
					# Collect available songs
					$runningSongs = Get-ChildItem -Path "$currentPath\running\"
					# Pick random number
					$number = Get-Random -minimum 0 -maximum ($runningSongs.Count -1)

					# Set music for this build
					$runningSound = $runningSongs[$number].FullName
					
					# Start music!
					Start-Process -FilePath $runningSound
					
					# Set music is playing
					$musicRunning = $true
					$runningBuild = $builds
				}
			}
			
			# Interval to check for running builds
			start-sleep 5
			
			# Collect running builds
			$apiCall = Invoke-RestMethod -Uri $url -Credential $cred
			$builds = $apiCall.builds.build
			
		# Do loop until there are no builds
		} until ($builds.Count -eq 0)
	
	# Build is complete and stop the music
	if ($musicRunning)
	{
		# By default Windows Media Player
		Stop-Process -processname wmplayer
		$musicRunning = $false
	}
	
	# Check for finished builds
	if ($runningBuild -ne $null)
	{
		# Base api for state of build
		$finishedUrl = $teamCityServer + $runningBuild.href
		
		# Collect results of build
		$finishedApiCall = Invoke-RestMethod -Uri $finishedUrl -Credential $cred
		$finishedBuild = $finishedApiCall.build
		
		# Succes state music else failure music
		if ($finishedBuild.status -eq "SUCCESS")
		{
			Start-Process -FilePath $successSound
			start-sleep 1
			Stop-Process -processname wmplayer
		} elseif ($finishedBuild.status -eq "FAILURE")
		{
			Start-Process -FilePath $failureSound
			start-sleep 15
			Stop-Process -processname wmplayer
		}
		# Reset running builds
		$runningBuild = $null
	}
	
	# Interval for complete loop
	start-sleep 10
	
} while ($endless)
</pre>
<h2>Starting the script</h2>
In order to start the script I used the Windows Task Schedule to start this script when a user is logged on. The machine I'm using is started automatically every morning and logged on directly so the script is started directly.
<h2>Conclusion</h2>
I think API's in products like TeamCity are very powerful and can be used for fun stuff. Maybe this example is not the most useful for an Agile development team but it still shows the power of an API. If have uploaded the complete project <a href="http://www.logitblog.com/downloads/TeamCity-Build-Music.zip" target="_blank">here</a> including some music so you can try this out.
