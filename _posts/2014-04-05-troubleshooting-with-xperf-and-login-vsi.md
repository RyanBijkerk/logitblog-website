---
layout: post
title: Troubleshooting with XPerf and Login VSI
authors: [ryan]
comments: true
categories: [Login VSI]
tags: [Login VSI, Performance Toolkit, Troubleshooting, XPerf]
image: assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/troubleshooting-with-process-monitor-and-login-vsi-feature-image.png
---
With integrating XPerf in a Login VSI workload you can get valuable information to troubleshoot a problem in your environment. In a previous <a href="http://www.logitblog.com/troubleshooting-with-process-monitor-and-login-vsi/" target="_blank">blog post</a> I described how to integrate Process Monitor in a Login VSI workload. This can also be done to other tools like XPerf. In this blog post I will explain how to integrate XPerf in a Login VSI workload to troubleshoot an issue.
<blockquote><strong>Please note:</strong> This is a personal blog and  not an official Login VSI blog post.</blockquote>
<h2>What is XPerf?</h2>
XPerf is a powerful tool that helps with overall system performance analysis. For more information I recommend to read <a href="http://blogs.technet.com/b/askperf/archive/2008/06/27/an-intro-to-xperf.aspx" target="_blank">this blog post</a>.

<span style="color: #000000; font-weight: bold;">Why is XPerf useful with Login VSI?</span>

Login VSI simulates users on a virtual desktop infrastructure environment (VDI) or a remote desktop session host environment (RDSH/SBC). Because Login VSI has simple workload customization you can quickly create a workload with a specific application that contains a problem. For troubleshooting you can add XPerf to get to the bottom of what the application is doing while Login VSI is using the application. By reproducing you can apply changes and rerun the workload to verify if the problem is solved. Because Login VSI can integrate in a production environment you are able to troubleshoot on a single target machine during office hours.
<blockquote>Please note: A Login VSI PRO license is required for workload customization. For more information please visit the <a href="http://www.loginvsi.com/" target="_blank">Login VSI website</a></blockquote>
<h2></h2>
<h2>Using XPerf with Login VSI</h2>
XPerf is part of the Windows Assessment and Deployment Kit and can be downloaded <a href="http://www.microsoft.com/en-US/download/details.aspx?id=39982" target="_blank">here</a>. Run the ADKsetup and download the installers.

<a href="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-ADK-Setup.png"><img src="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-ADK-Setup.png" alt="Xperf-ADK-Setup" width="300" height="220" /></a>
<h2>XPerf on the VSIshare</h2>
The best location to store the XPerf binaries is the VSIshare. We can use the default Login VSI mechanism to copy XPerf to your target machine.
<blockquote><strong>NOTE:</strong> Running XPerf from the VSIshare is possible but not recommend.</blockquote>
When the installers are downloaded you can install the WPTx86-x86_en-us.msi or WPTx64-x86_en-us on a machine to install XPerf.

<a href="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-WPT-Installers.png"><img src="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-WPT-Installers.png" alt="Xperf-WPT-Installers" width="300" height="212" /></a>

Copy the files in the "C:\Program Files (x86)\Windows Kits\8.0\Windows Performance Toolkit" directory. Probably not all files are required but I'm not sure which are so therefore I copied all files.

<a href="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-WPT-Installed.png"><img src="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-WPT-Installed.png" alt="Xperf-WPT-Installed" width="300" height="197" /></a>

To get XPerf on all the target machines we are going to create a directoy in the target libary. This way the files are copied when the Login VSI user logs on.

Browse to the VSIshare and create a direcoty called XPerf in the following location: {VSIshare}\_VSI_Binaries\Target\Lib\

<a href="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-VSI_Lib.png"><img src="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-VSI_Lib.png" alt="Xperf-VSI_Lib" width="300" height="216" /></a>

Paste the copied files.

<a href="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-VSI_Lib2.png"><img src="{{site.baseurl}}/assets/images/posts/2014-04-05-troubleshooting-with-xperf-and-login-vsi/Xperf-VSI_Lib2.png" alt="Xperf-VSI_Lib2" width="300" height="216" /></a>
<h2>Integration in the workload</h2>
XPerf is now copied on the target machine by the Login VSI logon script and can be used in the workload. In this example we are going to start XPerf, idle and stop the trace. Please use the command line reference for the correct trace.

<a href="http://msdn.microsoft.com/en-us/library/hh162920.aspx">http://msdn.microsoft.com/en-us/library/hh162920.aspx</a>

In this example where going to use the following command:
<pre class="brush: text; gutter: false">xperf -on DiagEasy+FILE_IO_INIT+REGISTRY</pre>
Because XPerf is not installed on the target machine we need to make sure the full path is used. The Login VSI command is as followed:
<pre class="brush: text; gutter: false">VSI_ShellExecute("XPerf", "%temp%\VSI\Runtime\Lib\Xperf\xperf.exe", "-on DiagEasy+FILE_IO_INIT+REGISTRY")</pre>
In this example where going to idle for amount of time. I'm using the Workload_Idle command which will display a message the XPerf trace is running.
<pre>Workload_Idle("XPerf", 600, "Running XPerf trace", 1)</pre>
Finally were are going to stop the trace. In this example were going to save the trace on the systemdrive in the Xperf directory. The file will contain the computer name so you can identify the trace file.
<pre class="brush: text; gutter: false">VSI_ShellExecute("XPerf", "%temp%\VSI\Runtime\Lib\Xperf\xperf.exe", "–d %systemdrive%\Xperf\%computername%-Xperf.etl")</pre>
After the trace is done the Login VSI user can logoff.
<pre class="brush: text; gutter: false">VSI_LogOff()</pre>
<h2>Running the workload</h2>
When starting a Login VSI test with the XPerf workload you will collect the Xperf date. This data can be used to troubleshoot a problem.

Complete XPerf workload:
<pre class="brush: text; gutter: false">#WLProp:Version#1.0.0
#WLProp:Date#2014-04-04
#WLProp:Description#The Logitblog XPerf Workload
##### Start segment Prepare
Segment("Workload", "Prepare")

# Random wait before starting workload
Workload_RandomIdle("Workload", 5, %VSI_RandomInitialWait%, "Waiting to start workload")

# Set mouse position
VSI_Mouse_Position("Workload")

##############################################################
##### Start segment 1 - XPerf   	                 #####
##############################################################
Segment("Workload", 1)

# Start XPerf Trace
VSI_ShellExecute("XPerf", "%temp%\VSI\Runtime\Lib\Xperf\xperf.exe", "-on DiagEasy+FILE_IO_INIT+REGISTRY")

# XPerf Idle
Workload_Idle("XPerf", 600, "Running XPerf trace", 1)

# Stop XPerf Trace
VSI_ShellExecute("XPerf", "%temp%\VSI\Runtime\Lib\Xperf\xperf.exe", "–d %systemdrive%\Xperf\%computername%-Xperf.etl")

# Logoff action
VSI_LogOff()</pre>
<h2>Conclusion</h2>
The combination of Login VSI and XPerf is perfect to troubleshoot a specifc problem. With the easy workload customization of Login VSI you can reproduce the problem over and over again. This way you can apply changes and verify if the problem is solved. With the detailed information from XPerf you have valuable information to find the cause of the problem.

If you have any comments or question please leave a comment below.
