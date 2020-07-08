---
layout: post
title: Inconsistencies with Citrix Cloud Remote PowerShell SDK
authors: [ryan]
comments: true
categories: [Citrix Cloud]
tags: [Citrix, Citrix, Citrix Cloud, Powershell]
image: assets/images/posts/2019-01-31-inconsistencies-with-citrix-cloud-remote-powershell-sdk/inconsistencies-with-citrix-cloud-remote-powerdhell-sdk-feature-image.png
---
Automation is key when doing performance testing. This is required to ensure performance tests are running in a clean state and produces consistent results. For a ICT-R research, additional automation was required in the base automation script for Citrix Cloud. But the behavior of the script was inconsistent using the PowerShell SDK for Citrix Cloud. This blog post will cover the issues encountered during this research.

## The goal and flow of the script
Let's first start with providing some details of the goal of the script. When doing a performance test the environment needs to be in a clean state. This means the hypervisor where the workload will be executed needs to be restarted. Any running machines need to be stopped before the reboot on the hypervisor is initiated. The steps are as follows:
<ol>
 	<li>Set the delivery group in maintenance;</li>
 	<li>Collect all machine from the delivery group;</li>
 	<li>Invoke power action to all machines in order to shutdown all the machines;</li>
 	<li>Ensure all machines are down before continuing to the next step;</li>
 	<li>Reboot the hypervisor;</li>
 	<li>Ensure the hypervisor is back online;</li>
 	<li>Disable the maintenance of the delivery group.</li>
</ol>
This script will be started from the management server where the Remote PowerShell SDK for Citrix Cloud is installed.
<h2>The inconsistencies</h2>
The script is executed from the management server which contains the latest Citrix Cloud Remote PowerShell SDK at that time which was around December 2018. Connecting to the Citrix Cloud instances and set the correct delivery group in maintenance mode works as expected. But for some reason, it was not able to collect all the machines. It produced the following error:
<pre class="lang:ps decode:true ">Get-BrokerMachine : The formatter threw an exception while trying to deserialize the message: There was an error while
trying to deserialize parameter http://tempuri.org/:records. The InnerException message was 'Invalid enum value
'L7_20' cannot be deserialized into type 'Citrix.Broker.Admin.SDK.FunctionalLevel'. Ensure that the necessary enum
values are present and are marked with EnumMemberAttribute attribute if the type has DataContractAttribute
attribute.'. Please see InnerException for more details.
At line:1 char:13
+ $desktops = Get-BrokerMachine -DesktopGroupName "Pool"
+ ~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Get-BrokerMachine], SdkOperationException
+ FullyQualifiedErrorId : Citrix.XDPowerShell.Broker.UnexpectedException,Citrix.Broker.Admin.SDK.GetBrokerMachineComman</pre>
When creating the Citrix Cloud integration I had validated all the steps and have seen it working. Thanks to a support case with Citrix a working solution was provided. The script needs to be executed on one of the Cloud Connectors. This worked but for other reasons, it is required to run the script from the management server. Therefore an Invoke-Command is added to run the PowerShell commands remotely on one fo the Cloud Connectors which will collect all the machines and ensure they are powered off. Unfortunately, during the script, it got stuck in the Invoke-Command section.

It took a lot of time to troubleshoot this behavior but in the end, it had to do with the remote pipeline towards the Cloud Connector machine. After configuring the WINRM specifying a MaxMemoryPerShell it worked how it should be.
<pre class="lang:ps decode:true ">winrm set winrm/config/winrs `@`{MaxMemoryPerShellMB=`"2048`"`}</pre>
<h2>Feedback on the Citrix Cloud Remote PowerShell SDK</h2>
When installing a Remote PowerShell SDK you can expect to remotely connect to the environment, as the name implies. For some reason, Citrix requires and recommends to run the scripts from a Cloud Connector which doesn't make sense. The statement from Citrix support is as follows:
<blockquote>Always run the PowerShell from the cloud connector where all the services are enabled and talking directly to cloud control plane.

<strong>Note: </strong>please see the update below.</blockquote>
There is also a downside at running the script at the Cloud Connector as it may update because Citrix manages updates to the Cloud Connector software, which may result in downtime. So taking that into account you need to ensure it is executed on the right available connector. While writing this blog post I have validated in the same environment and noticed it's working. This shows the benefit of Cloud technology as there is a continuous process in improvements and fixes.

<strong>UPDATE:</strong> Interesting fact, as <a href="https://twitter.com/MartinZugec" target="_blank" rel="noopener">Martin Zugec</a> pointed out this issue has to do with a mismatch of the Citrix Cloud Remote PowerShell SDK and Citrix Virtual Apps and Desktops service instance. Another important note is Citrix does not recommend to run the script on the Cloud Connectors.
<blockquote>Citrix recommends that you do not run this SDK’s cmdlets on Cloud Connectors. The SDK’s operation does not involve the Cloud Connectors.

<strong>Source</strong>: <a href="https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/sdk-api.html" target="_blank" rel="noopener">https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/sdk-api.html</a></blockquote>
<h2>Conclusion</h2>
Based on the feedback from Martin, if you encounter similar issues. ensure you are running the correct Citrix Cloud Remote PowerShell SDK. As stated it is not recommended to run the script on the Cloud Connector.

As always it is important to add proper error handling especially as Citrix is in control with pushing updates which may require updated SDK.

<del>If you encounter issues using the Citrix Cloud Remote PowerShell SDK try to run it on a Cloud Connector. But keep in mind the Cloud connectors may update during your script. It is important to understand Citrix manages all the update to the Cloud Connector software, this may result in breaking your script. So, please ensure the script contains error handling and in our case sending a notification to avoid losing valuable time in the ICT-R lab.</del>

Special thanks to <a href="https://twitter.com/eltjovg" target="_blank" rel="noopener">Eltjo van Gulik</a> for helping with troubleshooting these issues.

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/7FOSJVtUtac?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Jeremy Perkins</a> on <a href="https://unsplash.com/search/photos/cloud?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Unsplash</a></span>
