---
layout: post
title: App-V is not publishing
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, C#, Powershell]
image: assets/images/posts/2015-01-27-app-v-is-not-publishing/app-v-is-not-publishing.png
---
During Project VRC phase 7 we noticed some issues with publishing by App-V 5.0 Hotfix 4. Somehow App-V was not publishing during logon which is very strange. After some troubleshooting we found the root cause and shared this with the product team. With the release of Hotfix 5 I checked if this was solved, but to my surprise it was not. Within this blog post I will explain what is causing this issue and how to solve it.
<h2>What is going on?</h2>
When App-V is not publishing, the first place to take a look is the task scheduler. Within the task scheduler there is a task named "1_user_logon" which is located at "\Microsoft\AppV\Publishing". This task will make sure the sync is taking place when a user loggs on to the system. The event is both for user and global publishing. When there is a problem with publishing, the task will report this error "(0xFFFFFFE)" as the last run result. More detailed information can be found in the history tab. Event ID 201 will report the following text:

Task Scheduler successfully completed task "\Microsoft\AppV\Publishing\1_user_logon" , instance "{3567a710-c410-44a1-a435-6001a7b0ebe5}" , action "C:\Program Files\Microsoft Application Virtualization\Client\SyncAppvPublishingServer.exe" with return code 4294967294.

When the task is executed as expected the return code should be 0.

<a href="{{site.baseurl}}/assets/images/posts/2015-01-27-app-v-is-not-publishing/AppV-Task-Error.png"><img class="alignnone size-medium wp-image-3088" src="{{site.baseurl}}/assets/images/posts/2015-01-27-app-v-is-not-publishing/AppV-Task-Error.png" alt="" width="300" height="255" /></a>
<h2>What is the SyncAppVPublishingServer.exe?</h2>
With the release of Hotfix 4 the SyncAppvPublishingServer.exe is introduced which replaces the SyncAppvPublishingServer.vbs. Comparing the code of vbs and the exe (decompiled with <a href="https://www.jetbrains.com/decompiler/" target="_blank">JetBrains dotPeek</a>) both seem to be a wrapper that will execute the Sync-AppvPublishingServer command within powershell. The big difference is the way how powershell commands are executed. Within .NET there is the ability to directly use the powershell runtime which is a benefit on performance. This can be done with the RunspaceInvoke Class, more information can be found <a href="https://msdn.microsoft.com/en-us/library/system.management.automation.runspaceinvoke(v=vs.85).aspx" target="_blank">here</a>.
<h2>What is causing this issue?</h2>
As described in <a href="https://social.technet.microsoft.com/Forums/en-US/acfe8644-5101-4243-8e3f-0eb15107a1ca/appv-5-sp2-hf4-scheduled-tasks-failing-in-citrix-xendesktop-environment?forum=mdopappv" target="_blank">this</a> forum post the error is caused by powershell. Within the SyncAppvPublishingServer.exe the following command is run: "Set-ExecutionPolicy RemoteSigned -Scope Process". When executing the SyncAppvPublishingServer.exe there is no feedback within the console or any information in the event log. Therefore I rebuild the solution and returned the exception to understand what really is going on. Running my own version of SyncAppvPublishingServer.exe, I got the following powershell error:
<blockquote>Windows PowerShell updated your execution policy successfully, but the setting is overridden by a policy defined at a more specific scope.
Due to the override, your shell will retain its current effective execution policy of Unrestricted. Type "Get-ExecutionPolicy -List" to view your execution policy settings.
For more information please see "Get-Help Set-ExecutionPolicy".</blockquote>
And the following C# exception is thrown:
<blockquote>System.Security.SecurityException: Security error.
at System.Management.Automation.MshCommandRuntime.ThrowTerminatingError(ErrorRecord errorRecord)
--- End of inner exception stack trace ---
at System.Management.Automation.Runspaces.PipelineBase.Invoke(IEnumerable input)
at System.Management.Automation.RunspaceInvoke.Invoke(String script, IEnumerable input)
at System.Management.Automation.RunspaceInvoke.Invoke(String script)
at Test.Program.Main(String[] args)</blockquote>
<h2>Solution</h2>
As the powershell error described the execution policy is overwritten. The security exception indicates the process needs to run with higher privileges. I noticed this error is related to the group policy that is configured. The policy "Administrative Templates/Windows Components/Windows PowerShell/Turn on Script Execution" can be configured both on user and computer level. When this policy is configured to "Allow all scripts" on the user level the exception within SyncAppvPublishingServer.exe is thrown. There a two ways to solve the error.
<ul>
 	<li>Change the group policy setting to "Allow local scripts and remote signed scripts"</li>
 	<li>Configure the policy on a computer level</li>
 	<li>Update to SP3</li>
</ul>
I hope this provides a better understanding why this error occurs and how to solve it. If you have any comments or questions please leave them below.
<h2>Update</h2>
According to the comment by Roy Essers the above issue has been solved with App-V SP3 release. Based on this feedback I took a second look and can confirm the publishing issue is fixed. A try catch has been added when the execution policy for the process is set which solves the issue that App-V is not publishing.
