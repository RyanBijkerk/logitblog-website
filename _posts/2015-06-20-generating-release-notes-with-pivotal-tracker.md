---
layout: post
title: Generating release notes with Pivotal Tracker
authors: [ryan]
comments: true
categories: [Development]
tags: [API, Automation, Development, Pivotal Tracker, Powershell]
image: assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/generating-release-notes-with-pivotal-tracker-feature-image.png
---
When developing a product it is common to create release notes when there is an update of the product. Creating these release notes can be a time consuming process which is depending on the amount of user stories that are delivered. It is common that Agile Project Management solutions like <a href="http://www.pivotaltracker.com" target="_blank">Pivotal Tracker</a> have an API that can be used to retrieve information. In this blog post I will explain how the API of Pivotal Tracker can be used to generate release notes in an Excel sheet.

<h2>Retrieve information<a href="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/profile-menu-bar.png"><img class="alignright size-full wp-image-3281" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/profile-menu-bar.png" alt="profile-menu-bar" width="227" height="207" /></a></h2>
To collect the information from the API of Pivotal Tracker a API token is needed. This token will be used for the authentication. Go to you profile and click create new token.

<a href="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/profile-token.png"><img class="alignnone wp-image-3282 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/profile-token.png" alt="profile-token" width="270" height="300" /></a>

Within the script the token needs to be specified including the project number which can be found in the main URL of you project.<img class=" size-medium wp-image-3283 alignright" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/project-number.png" alt="project-number" width="300" height="18" />
<pre class="lang:ps decode:true"># Pivotaltracker API token, can be found in the profile page
$apiKey = "APIKEY"
# Pivotaltracker Project number
$pivotalProject = "ProjectNumber"
</pre>
<h2>Pivotal structure</h2>
This script is created on a specific structure within Pivotal Tracker. The user stories between two release are retrieved for the release notes.<a href="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/main-demo-project.png"><img class="alignnone wp-image-3279 size-medium" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/main-demo-project.png" alt="main-demo-project" width="300" height="118" /></a>

These release needs to specified in the script.
<pre class="lang:ps decode:true"># Release milestones to collect the userstory between
$releaseFrom = "Release 1.0.0"
$releaseUntil = "Release 1.0.1"
$releaseNumber = "1.0.1"</pre>
The release notes will be generated in an Excel sheet, therefor the location of the Excel sheet needs to be specified. Please note the script will expect an existing sheet.
<pre class="lang:ps decode:true"># Release notes excel file
$releaseNotesSheet = "D:\Temp\Demo-release-notes.xlsx"</pre>
Within a user story the label note can be used to mark it. If a user story is marked it will be set as a public release note. By adding the text "Release note:" the script will add this text to the Excels sheet. This way you can make more user friendly release notes which can be used publicly.

<a href="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/feature-with-note.png"><img class=" size-medium wp-image-3278 alignnone" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/feature-with-note.png" alt="feature-with-note" width="300" height="276" /></a>

By default the script will exclude chores because these are not relevant for release notes.
<h2>Creating the release notes</h2>
At first the release needs to be collect so we can define the range of user stories that needs to be collected.
<pre class="lang:ps decode:true "># API call for the milestones
$url = "https://www.pivotaltracker.com/services/v5/projects/" + $pivotalProject + "/stories?&amp;filter=story_type%3Arelease"
$releases = Invoke-RestMethod -Uri $url -Header @{"X-TrackerToken" = "$apiKey"}

# Create array for the found releases
$releaseIds =@()
foreach ($release in $releases)
{
	if ($release.Name -eq $releaseFrom -Or $release.Name -eq $releaseUntil)
	{
		$releaseIds = $releaseIds + $release.id
	}
}
Write-Host "Number of releases:" $releaseIds.Count</pre>
The script expects the Excel sheet to exists and is going to retrieve the existing items.
<pre class="lang:ps decode:true "># Check if excel sheet can be accessed
If (Test-Path -Path $releaseNotesSheet)
{
	# Create the excel object and opening the excel sheet
	$Excel = New-Object -Com Excel.Application
	$Excel.Visible = $True # Set to false to edit excel sheet in the background
	$WorkBook = $Excel.Workbooks.Open("$releaseNotesSheet")
	$WorkSheet = $WorkBook.Worksheets.Item(1)
} 
Else
{
	Write-Host "Cannot access:" $releaseNotesSheet
	Timeout 10
	Exit(2)
}

# Create array with existing PivotalIDs
$PivotalIDs =@()
$i = 2
$beginRows = $WorkSheet.UsedRange.Rows.Count
Write-Host "Collecting existing items"
do
{
	$value = $worksheet.cells.item($i,2).Text
	if ($value -ne "" -Or $value -ne "PivotalID")
	{
		$PivotalIDs = $PivotalIDs + $value
	}
	$i++
} while ($i -le $beginRows)</pre>
If the information is correct and there are only two releases the next step is to retrieve the user stories and add them to the Excel sheet. This is the biggest part and the script will go through all the user stories and add them to the Excel sheet.
<pre class="lang:ps decode:true"># Check for amount of releases in the array (should be only two)
if ($releaseIds -le 1)
{
	Write-Host "Cannot find the specified releases"
	Timeout 10
	Exit(3)
} 
else
{
	# API call for the userstories between selected milestones
	$url = "https://www.pivotaltracker.com/services/v5/projects/" + $pivotalProject + "/stories?after_story_id=" + $releaseIds[0] + "&amp;before_story_id=" + $releaseIds[1]
	$userStories = Invoke-RestMethod -Uri $url -Header @{"X-TrackerToken" = "$apiKey"}
	
	# Array for blocked labels
	$blockedLabels = @()
	$blockedLabels = $blockedLabels + "note"
	$blockedLabels = $blockedLabels + "bug"
	$blockedLabels = $blockedLabels + "timebox"

	foreach ($userStory in $userStories)
	{
		# Display the ID of the selected user story
		Write-Host "Working on user story:" $userstory.id -NoNewline
		
		# Check if userstoryid already is added
		$addItem = $true
		foreach ($id in $PivotalIDs)
		{
			if ($id -eq $userStory.id)
			{
				# Yes it is!
				$addItem = $false
				break
			}
		}
		
		# Add item is not already exists
		if($addItem)
		{
			# Only add when the story type is a feature of a bug
			if ($userStory.story_type -eq "feature" -Or $userStory.story_type -eq "bug")
			{
				# Column format
				# 1,		2,			3,					4,		5,			6,		7
				# Version,	PivotalID,	Pivotal subject,	Type,	Component,	Public,	Description	
				
				# Check for amount of rows in the excel sheet
				$rows = $WorkSheet.UsedRange.Rows.Count + 1
				
				# Add the release number to the column 1
				$worksheet.cells.item($rows,1) = $releaseNumber
				
				# Add user pivotal ID and hyperlink url in column 2
				$worksheet.cells.item($rows,2) = $userStory.id
				$hyperlinkCell = "B" + $rows
				$hyperlinkCell = $worksheet.Range($hyperlinkCell)
				$worksheet.Hyperlinks.Add($hyperlinkCell, $userStory.url) | Out-Null
				
				# Add the pivotal subject to column 3
				$worksheet.cells.item($rows,3) = $userStory.Name
				
				# Add the type to column 4
				$worksheet.cells.item($rows,4) = $userStory.story_type
				
				# Set default public note to false
				$publicNote = $false
				
				# Create the empty component string for all the labels
				$compnentString = ""
				
				# Go into the label loop when user story contains labels
				if ($userStory.labels.count -gt 0)
				{
					# Loop trough the labels
					foreach ($label in $userStory.labels)
					{
						# When label is equal to note set the public note to true
						if ($label.Name -eq "note")
						{
							$publicNote = $true
						}
						
						# Set the default for adding a label to true
						$addLabel = $true
						
						# Loop trough the blocked labels
						foreach ($blockedLabel in $blockedLabels)
						{
							# If the label is equal to the blocked label set the adding a label to false
							if ($blockedLabel -eq $label.Name)
							{
								$addLabel = $false
							}
						}
						
						# If adding the label is true add label to the component string
						if ($addLabel)
						{
							$compnentString = $compnentString + $label.Name + ", "
						}
					}
				}
				
				# If the component string contains characters trim the last , &lt;space&gt;
				if ($compnentString.length -gt 0)
				{
					$compnentString = $compnentString.Substring(0,$compnentString.Length-2)
				}
				
				# Add the component string to column 5
				$worksheet.cells.item($rows,5) = $compnentString
				
				# If public note is true add Yes to column 6, if not add No to column 6 and don't add a release note
				if ($publicNote)
				{
					$worksheet.cells.item($rows,6) = "Yes"
					
					# Collect the description for a public release note
					$releaseNotePosition = $userStory.description.ToLower().IndexOf("release note:")
					
					# When the release note is found add the release note to column 7
					if ($releaseNotePosition -ne "-1")	
					{
						$releaseNote = $userStory.description.Substring($releaseNotePosition +14)
						$worksheet.cells.item($rows,7) = $releaseNote
					}	
				}
				else
				{
					$worksheet.cells.item($rows,6) = "No"
				}
			}
			else
			{
				Write-Host " - Item is not a feature or a bug"
				Write-Host ""
			}
		}
		else
		{
			Write-Host " - Item already exists"
			Write-Host ""
		}
	}</pre>
Finally a bar is defined as the end of the release and the Excel sheet is saved and closed.
<pre class="lang:ps decode:true ">	# Is no new rows are added don't add release bar
	if ($beginRows -ne $WorkSheet.UsedRange.Rows.Count)
	{
		# Add the Release bar with white text and blue background color
		$worksheet.cells.item($rows + 1,1) = "Release"
		$worksheet.cells.item($rows + 1,1).Font.Bold = $true
		$worksheet.cells.item($rows + 1,1).Font.ColorIndex = 2
		$worksheet.Cells.Item($rows+1, 1).EntireRow.Interior.ColorIndex = 49
	}
	
	# Save the excel sheet
	$WorkBook.Save()
	$Excel.Quit()
}</pre>
<h2>Result</h2>
For this blog post I created a small example project which has a couple user stories. The script will add the following details to the Excel sheet.
<ul>
 	<li>Version = Specified in the script</li>
 	<li>Pivotal ID = Link toward the specific user story</li>
 	<li>Pivotal subject = The tile of the user story</li>
 	<li>Type = Feature or a bug</li>
 	<li>Component = Labels</li>
 	<li>Public = Public release note, Yes or No</li>
 	<li>Description = User friendly release note if specified</li>
</ul>
<h2><a href="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/excel-release-notes.png"><img class="alignnone size-medium wp-image-3292" src="{{site.baseurl}}/assets/images/posts/2015-06-20-generating-release-notes-with-pivotal-tracker/excel-release-notes.png" alt="excel-release-notes" width="300" height="109" /></a></h2>
<h2>Conclusion</h2>
By using the API of Pivotal Tracker we are able to retrieve all the information so we can automatically generate the release notes. As a Product Owner there is one place to keep all the information including the release notes of specific user stories. I hope this gives you a head start by automating your own release notes. A full copy of the script can be found <a href="http://www.logitblog.com/downloads/Pivotal-Release-Notes-blogpost.txt" target="_blank">here</a>. If you have questions or comments please leave them below.
