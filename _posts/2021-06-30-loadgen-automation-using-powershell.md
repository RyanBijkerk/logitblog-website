---
layout: post
hidden: false
title: "LoadGen automation using PowerShell"
authors: [ryan]
categories: ['LoadGen']
tags: [LoadGen, PowerShell, Code]
image: assets/images/posts/2021-06-30-loadgen-automation-using-powershell/loadgen-automation-using-powersehll-feature-image.png
---
Automation is key when performing a lot of LoadGen tests. Using the automation ensure consistency and will reduce time. At GO-EUC automation is heavily used to continuously execute LoadGen performance tests. This blog post will share how to start and monitor a LoadGen test using PowerShell.

## What is LoadGen?
For those who are not familiar with LoadGen, let's first start introducing it.

LoadGen offers customers and service providers a complete software solution to test, and maintain the optimal performance, user experience, scalability, and availability of (virtual) desktop environments including all of your business applications.
Whatever you wish to test, LoadGen makes it easier with our user-friendly software. Load tests, stress tests, performance tests, End-to-End Monitoring. The wide range of Load and Performance Testing options allows you to create customized simulations in just a few simple steps. The outcome means that if necessary, you can take appropriate action well in time so that the user experience of your IT environment remains optimal. LoadGen simulates end-user behavior for systems such as Citrix Virtual Apps and Desktops, Microsoft Remote Desktop Services, VMware Horizon, and Fat Clients.

Product and Application reliability and objective simulations that identify the possible effects of planned changes to your IT environment. It allows you to test what will happen before the actual change is applied, allowing you to gain full control over the performance of your IT environment and your end-users to continue using your systems unhindered.

More information about LoadGen can be found [here](https://www.loadgen.com/){:target="_blank"}.

## The steps that are required
Before going into the PowerShell code details, let's start by explaining the steps and requirements. In terms of requirements, this example requires a full setup and configured LoadGen environment which is ready to start a test. Also please be aware LoadGen requires to run in an interactive session, meaning it cannot be executed using a background process or services.

The example will do the following steps:

  * Validate any running LoadGen Director process
  * Start LoadGen test based on the canvas
  * Monitor for new the database table in LoadGen database
  * Collect scenario and user configuration
  * Monitor launched users in the database

These steps are all done using PowerShell and require the SQLServer module in Powershell, which can be installed by the following command:

```PowerShell
Install-Module -Name SqlServer
```
Source: [PowerShell Gallery | SqlServer](https://www.powershellgallery.com/packages/Sqlserver){:target="_blank"}.

## PowerShell code
The code has been split into two functions, the first for starting the LoadGen test and the second for monitoring the launched sessions.

Starting the test requires the parameter LoadGen canvas name, SQL server, and LoadGen database name.

```PoweShell
Function Start-Test {
    Param( 
        [string]$Canvas,
        [string]$SqlSever,
        [string]$Database )
    
    $startDate = Get-Date


    Write-Host (Get-Date) ": Validating on running instance of LoadGen..."
    $loadGenProcess = Get-Process "LoadGen Director" -ErrorAction SilentlyContinue

    if ($loadGenProcess) {
        Write-Host (Get-Date) ": LoadGen still running, closing it..."
        $loadGenProcess | Stop-Process -Force
    }
    
    Write-Host (Get-Date) ": Starting LoadGen Test..."
    Start-Process -FilePath "$env:ProgramFiles\LoadGen\LoadGen Director\LoadGen Director.exe" -ArgumentList "/nvl:true /nvc /start:now /canvas:$Canvas" -WindowStyle Minimized

    $queryDatabase = "SELECT name, create_date FROM $($DataBase).sys.tables WHERE name LIKE 'SMD%' ORDER BY create_date DESC"

    Write-Host (Get-Date) ": Waiting for database table to be created..."

    $i = 0;
    while (!$tableName) {

        $tables = Invoke-Sqlcmd -Query $queryDatabase -ServerInstance "flow-sql-1"
        $tableName = ($tables | Where-Object {$_.create_date -ge $startDate}).name

        Start-Sleep 5

        if ($i -gt 30){
            Write-Error (Get-Date) ": Could not find the table..."
        }
        $i++
    }

    Write-Host (Get-Date) ": Table found with name $tableName"

    return $tableName
}
```
the following function si their monitoring function, which requires the same parameters plus the table name that has been returned from the previous function.

```PowerShell
Function Monitor-Test {
    Param( 
        [string]$TableName,
        [string]$Canvas,
        [string]$SqlSever,
        [string]$Database )

    Write-Host (Get-Date) ": Monitor LoadGen test."
    Write-Host (Get-Date) ": Using table $TableName"

    $loadGenProf = "C:\ProgramData\LoadGen\Common\LoadGenProfiles.xml"
    $loadGenDir = "C:\ProgramData\LoadGen\Common\LoadGenDirector.xml"
    
    [xml]$loadGenProfConfig = Get-Content $loadGenProf
    [xml]$loadGenConfig = Get-Content $loadGenDir
    $scenario = ($loadGenProfConfig.LoadProfilesDataset.LoadProfiles | Where-Object {$_.profileName -eq $Canvas }).ActiveScenario
    
    $sessions = ($loadGenConfig.MainConfiguration.Scenarios | Where-Object {$_.Name -eq $scenario}).Sessions
    [int]$duration = ($loadGenConfig.MainConfiguration.Scenarios | Where-Object {$_.Name -eq $scenario}).TotalDuration

    $queryLaunchedSessions = "SELECT ISNULL(COUNT(Username),'') [Initializing] FROM [$Database].[dbo].[$TableName] WHERE Type = 'DenamikInitializing'" 
    
    $timeoutTime = ([int]$duration + 15)
    $startTime = Get-Date
    $timeout = $false
    
    if (!($timeout)) {
        
        $launchedSessions = 0
        
        while($launchedSessions -lt $sessions) {
            
            $launchedSessions = (Invoke-Sqlcmd -Query $queryLaunchedSessions -ServerInstance "flow-sql-1").Initializing
            Write-Host (Get-Date) ": $launchedSessions of $sessions sessions launched."

            Start-Sleep -Seconds 30

            if (((Get-Date) - $startTime).TotalMinutes -gt $timeoutTime) {
                $timeout = $true
                break
            }
        }
    }

    if ($timeout) {
        Write-Host (Get-Date) ": Timeout reached!"
    } else {
        Write-Host (Get-Date) ": All sessions launched."

        $durMin = [math]::Round((Get-Date).Subtract($startTime).TotalMinutes)
        if ($duration -gt $durMin) {
            $idleTime = [math]::Round($duration - $durMin) + 5
            
            Write-Host (Get-Date) ": Waiting $idleTime min. for the loop to finish..."
            $idleTimeSec = $idleTime * 60
            Start-Sleep -Seconds $idleTimeSec  
        } else {
            Write-Host (Get-Date) ": Waiting 5 min. for the loop to finish..."
            Start-Sleep -Seconds 300
        }

    }

    try {
        Get-Process "LoadGen Director" | Stop-Process
    } catch {
        Write-Error (Get-Date) ": Something went wrong while closing LoadGen Director..."
    }
}
```

The following example shows how both functions can be used:

```PowerShell
$databaseTable = Start-Test -SqlServer "sql-1" -Database "LoadGen" -Canvas "Default_Citrix"

Monitor-Test -SqlServer "sql-1" -Database "LoadGen" -Canvas "Default_Citrix" -TableName $databaseTable
```

## Conclusion
Repeatability is an important factor when executing a lot of performance tests. By using PowerShell it is possible to automated starting and monitoring a LoadGen test. When including this in total automation it is possible to continuously execute and monitor tests, just like at GO-EUC.

When you have questions or comments, please leave them below.

Photo by <a href="https://unsplash.com/@clayton_cardinalli?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Clayton Cardinalli</a> on <a href="https://unsplash.com/s/photos/automation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Unsplash</a>
  