---
layout: post
title: Scaling Development Teams with Pivotal Tracker
authors: [ryan]
comments: true
categories: [Development]
tags: [API, Development, LeSS, Pivotal Tracker, Powershell, Scrum]
image: assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/scaling-development-teams-with-pivotal-tracker-feature-image.png
---
Scaling your development capacity can result in having multiple Development Teams. When using Scrum, the intent is to have one Product Owner managing one Product Backlog. With multiple Development Teams working on the same product, each Development Team has their own Sprint Backlog that they commit to for the duration of the iteration – the Sprint.

In turned out that Pivotal Tracker is not supporting this out of the box – One Product Backlog, multiple Sprint Backlogs. In this blog post I'm going to share how to organize Pivotal Tracker to support multiple Development Teams working on one Product Backlog.

<h2>LeSS Framework</h2>
I came across this challenge with my colleague <a href="http://nl.linkedin.com/in/matthijsdebooij" target="_blank">Matthijs de Booij</a> while investigating different Agile scaling frameworks. Based on our context we decided to experiment with the LeSS Framework.
<blockquote>Large-Scale Scrum (LeSS) is Scrum applied to many teams working together on one product. LeSS is (1) lightweight, (2) simple to understand, and (3) difficult to master—due to essential complexity.</blockquote>
&nbsp;

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Less-framework.png"><img class="alignnone wp-image-3367 size-large" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Less-framework.png" alt="LeSS Framework" width="584" height="279" /></a>

During the investigation of the LeSS framework one of many topics we addressed was tooling to manage the Product Backlog. On the <a href="http://less.works" target="_blank">LeSS.works website</a> the following information is posted about Product Backlog tools:
<blockquote>Use simple tools for the Product Backlog. We’ve seen LeSS Huge initiatives (with thousands of people) manage the Product Backlog with a simple spreadsheet. Avoid unnecessary and costly complication by using the simplest tools possible for managing the Product Backlog.

&nbsp;

Here is a side note regarding tool usage in Scrum and in LeSS. It is common for organizations to look for tools to solve their problems even though tools are rarely the cause of the problem. Avoid solving problems with tools unless you truly, deeply understand the problem and consider a tool to be the the right solution for that particular problem.

Source: <a href="https://less.works/less/framework/product-backlog.html#product-backlog-tools" target="_blank">LeSS.Works</a></blockquote>
<h2></h2>
There is no real suggestions for tooling that works well with the LeSS framework. Since we have good experience with <a href="http://www.pivotaltracker.com" target="_blank">Pivotal Tracker</a> we wanted to find out if it could fulfill our needs without making it unnecessary complicated.
<h2>Product Backlog</h2>
So the goal is to manage one Product Backlog (Pivotal Tracker calls this a project backlog) with multiple Sprint Backlogs. In Pivotal Tracker it is not possible to assign multiple teams to one Product Backlog, but there is the possibility to create workspaces that allows you to combine multiple "projects" in one overview. Within the workspace it is possible to move user stories from one project to another. The first step is to create a Pivotal project for the main Product Backlog and projects for each Development Team, serving as Sprint Backlogs.
<h2>Creating a workspace</h2>
On the main dashboard of Pivotal Tracker you can create a workspace. When creating the workspace you need to provide a name.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal.png"><img class="alignnone size-medium wp-image-3380" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal.png" alt="Projects-pivotal" width="300" height="158" /></a> <a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal-workspace.png"><img class="alignnone size-medium wp-image-3381" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal-workspace.png" alt="Projects-pivotal-workspace" width="300" height="133" /></a>

After creating a workspace you have the ability to add projects and save this. The end result will be the current/backlog of the added projects in one overview.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace.png"><img class="alignnone wp-image-3359 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace.png" alt="Pivotal Workspace Add" width="99" height="300" /></a> <a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-add.png"><img class="alignnone wp-image-3360 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-add.png" alt="Pivotal Workspace Projects" width="73" height="300" /></a> <a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-added.png"><img class="alignnone size-medium wp-image-3382" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-added.png" alt="Pivotal-workspace-added" width="74" height="300" /></a>

<strong>Please note:</strong> all the project needs to be configured the same like estimations scale otherwise it is not possible to move user stories to a different project.
<h2>User story flow</h2>
During <a href="http://less.works/less/framework/sprint-planning-one.html" target="_blank">Sprint Planning One</a> the users stories of the Product Backlog will be distributed over the Sprint Backlogs for the upcoming sprint. Within the workspace you can move the user stories to the teams. After this is done, each Development Team can continue with <a href="http://less.works/less/framework/sprint-planning-two.html" target="_blank">Sprint Planning Two</a>.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story.png"><img class="alignnone size-medium wp-image-3383" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story.png" alt="Pivotal-workspace-moving-story" width="300" height="63" /></a> <a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story-moved.png"><img class="alignnone size-medium wp-image-3385" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story-moved.png" alt="Pivotal-workspace-moving-story-moved" width="300" height="63" /></a>
<h2>Velocity</h2>
For a Product Owner the velocity is an important metric. The velocity allows the Product Owner to prepare the upcoming sprints and provide timelines to stakeholders. This gets even more important when multiple teams are working on the same Product Backlog. Because user stories are moved from the Product Backlog to a Sprint Backlog , there won't be an overall velocity available. Velocity will build up in each individual Sprint Backlogs because this is where the value is delivered. Within the Product Backlog there is no velocity and this will make it hard to prepare for the upcoming sprints and provide timelines. Because this functionality in unavailable in Pivotal Tracker, we created a work-around to force the velocity in the Product Backlog. By resetting the starting date of the Product Backlog each sprint and setting the initial starting velocity to the total velocity of all the teams you will get an estimation of how much can be delivered during a Sprint. This is very useful for Release and Sprint Planning.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal-properties.png"><img class="alignleft size-medium wp-image-3387" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Projects-pivotal-properties.png" alt="Projects-pivotal-properties" width="300" height="217" /></a>

&nbsp;
<h2><a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-velocity.png"><img class="alignleft size-medium wp-image-3386" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-velocity.png" alt="Pivotal-workspace-velocity" width="300" height="148" /></a></h2>
<h2>Automation</h2>
Resetting the velocity in the Product Backlog every Sprint can be annoying but luckily Pivotal Tracker has a great API which we can use for adjusting the settings. In order to automatically reset the start date and velocity of the Product Backlog you can use this powershell script.
<pre class="lang:ps decode:true"># Pivotaltracker API token, can be found in the profile page
$apiKey = "APIKEY"
# Pivotaltracker Project number
$mainPivotalProject = "00000000"

$sprintDays = 14

# Team name started
$pivotalTeams = @()
$pivotalTeams += "Sprint Backlog Team Blue"
$pivotalTeams += "Sprint Backlog Team Green"

# Collect all team projects
$url = "https://www.pivotaltracker.com/services/v5/projects/?fields=name,current_velocity"
$pivotalProjects = Invoke-RestMethod -Uri $url -Header @{"X-TrackerToken" = "$apiKey"}

$totalVelocity = 0

function Ask-Question($question)
{
	$exit = $false
	do
	{
		$confirmation = (Read-Host $question "[y/n]").ToLower()
				
		if ($confirmation -eq "y")
		{
			$exit = $true
			$return = $true
		} elseif ($confirmation -eq "n")
		{
			$exit = $true
			$return = $false
		} else
		{
			Write-Host "Invalid input, please try again"
		}
		
	} While (!$exit)
	
	return $return	
}

# Loop to set team projects
Foreach ($pivotalProject in $pivotalProjects)
{
	Foreach ($pivotalTeam in $pivotalTeams)
	{
		if ($pivotalProject.Name -eq $pivotalTeam)
		{
			Write-Host "Team:" $pivotalProject.Name
			Write-Host "Velocity:" $pivotalProject.current_velocity
			$totalVelocity = $totalVelocity + $pivotalProject.current_velocity
		}
	}
}

# API call for the milestones
$url = "https://www.pivotaltracker.com/services/v5/projects/" + $mainPivotalProject 
$mainProject = Invoke-RestMethod -Uri $url -Header @{"X-TrackerToken" = "$apiKey"}

# Collect and detemin new start date of main backlog project
$mainProjectCurrentStartDate=[datetime]::ParseExact($mainProject.start_date, "yyyy-MM-dd", $null)
$mainProjectNewStartDate = $mainProjectCurrentStartDate.AddDays($sprintDays).ToString("yyyy-MM-dd")

Write-Host "New start date:" $mainProjectNewStartDate

# Current date
$currentDate = Get-Date -Format "yyyy-MM-dd"

# Check if start date is in the future
if ($mainProjectNewStartDate -gt $currentDate)
{
	Write-Host "Start date of the main backlog will be set in the future"
	
	$return = Ask-Question "Do you want to continue and apply the change?"
	
	if (!$return)
	{
		Write-Host "Script will exit"
		exit
	}
	
}


if ($confirmation -eq 'y') {
  # proceed
}

# Set new date including time otherwise put method wont work
$mainProjectNewStartTime = $mainProjectNewStartDate + "T07:00:00:00Z"

# Create body for json
$body = @{
start_date = $mainProjectNewStartDate
start_time = $mainProjectNewStartTime
initial_velocity = $totalVelocity
}

# Converting to json
$json = $body | ConvertTo-Json

# Put method to update main backlog project
$response = Invoke-RestMethod $url -Method Put -Body $json -ContentType 'application/json' -Header @{"X-TrackerToken" = "$apiKey"}</pre>
When running the script output will be displayed and Pivotal Tracker provides a message to reload the workspace because of changes.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-project-reload.png"><img class="alignnone size-medium wp-image-3388" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-project-reload.png" alt="Pivotal-project-reload" width="300" height="106" /></a>
<h2>Conclusion</h2>
When multiple Development Teams are working on one Product Backlog, you want each Development Team to have their own Sprint Backlog, and you will need the overall estimated velocity in order to plan Sprints and releases.

<a href="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story-moved.png"><img class="alignleft size-medium wp-image-3385" src="{{site.baseurl}}/assets/images/posts/2015-09-28-scaling-development-teams-with-pivotal-tracker/Pivotal-workspace-moving-story-moved.png" alt="Pivotal-workspace-moving-story-moved" width="300" height="63" /></a>

I would love to have this supported in Pivotal Tracker out of the box, for example by linking projects and combining the velocity in the Product Backlog. Luckily Pivotal Tracker has an API which can be used to apply the work around. I hope you find this useful and if you have questions or comments please feel free to contact me.
