---
layout: post
title: Troubleshooting with Process Monitor and Login VSI
authors: [ryan]
comments: true
categories: [Login VSI]
categories: [Login VSI, Process Monitor, Procmon, Troubleshooting, Workload]
image: assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/troubleshooting-with-process-monitor-and-login-vsi-feature-image.png
---
Troubleshooting with Process Monitor and Login VSI is a powerful combination. When troubleshooting a problem you often want to reproduce the same behavior. This is possible with the workload customization in Login VSI. With tools like Process Monitor you can identify problems which can help you find the root cause. In this blog post I will provide an example how to integrate Process Monitor in Login VSI.
<blockquote><strong>Please note:</strong> This is a personal blog and  not an official Login VSI blog post.</blockquote>
<span style="color: #000000; font-weight: bold;">Why is this useful with Login VSI?</span>

Login VSI simulates users on a virtual desktop infrastructure (VDI) or remote desktop session host (RDSH/SBC) environment. Because Login VSI has simple workload customization you can quickly create a workload with a specific application that contains a problem. For troubleshooting you can add Process Monitor to get to the bottom of what the application is doing while Login VSI is using the application. By reproducing you can apply changes and rerun the workload to verify if the problem is solved. Because Login VSI can integrate in a production environment you are able to troubleshoot on a single target machine during office hours.
<blockquote>Please note: A Login VSI PRO license is required for workload customization. For more information please visit the <a href="http:// www.loginvsi.com" target="_blank">Login VSI website</a>

&nbsp;</blockquote>
<h2>Using Process Monitor with Login VSI</h2>
With Process Monitor you can monitor application behavior on file system- and registry-level. For more information and the download please visit the <a href="http://technet.microsoft.com/en-us/sysinternals/bb896645.aspx" target="_blank">Microsoft website</a>. Process Monitor can be run by the command line and therefore it is simple to use in a Login VSI workload.
<blockquote><b>Please note:</b> Process Monitor requires administrative privileges.</blockquote>
<h2></h2>
<h2>Process Monitor on the VSIshare</h2>
The first step is to download Process Monitor. After downloading Process Monitor browse to the following location "{VSIshare}\_VSI_Binaries\Target\Lib" and create a directory "Procmon". Copy the downloaded Process Monitor executable to the "Procmon" directory. This way Login VSI will copy Procmon.exe when a Login VSI user logs on.

<a href="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-dircreate.png"><img class="alignnone size-medium wp-image-2668" style="color: #333333; font-style: normal; line-height: 24px;" src="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-dircreate.png" alt="Procmon-dircreate" width="300" height="207" /></a> <a href="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-copy.png"><img class="alignnone size-medium wp-image-2669" src="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-copy.png" alt="Procmon-copy" width="300" height="207" /></a>

<span style="color: #000000; font-weight: bold;">Process Monitor integration in the workload</span>

The next step is to integrate Process Monitor in the workload. First we need to know which parameters we need to use. This can be done by running Process Monitor from the command line with the /? parameter.

<a href="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-parameters.png"><img class="alignnone size-medium wp-image-2671" src="{{site.baseurl}}/assets/images/posts/2013-12-12-troubleshooting-with-process-monitor-and-login-vsi/Procmon-parameters.png" alt="Procmon-parameters" width="300" height="281" /></a>

To start a trace we can use the following command line:
<blockquote>Procmon.exe /AcceptEula /Quiet /Minimized /BackingFile “C:\Example.pml”</blockquote>
Within the workload we use the VSI_ShellExecute command to start Process Monitor. For all the Login VSI meta language commands please visit the <a href="http://www.loginvsi.com/documentation/" target="_blank">documentation website</a>.
<pre>VSI_ShellExecute("Procmon", "%temp%\VSI\Runtime\Lib\Procmon\Procmon.exe", "/AcceptEula /Quiet /Minimized /BackingFile %systemdrive%\%computername%-Procmon.pml")</pre>
After we started a trace you need to add the application you want to troubleshoot. To stop the Process Monitor trace we have to add the following command to the workload.
<pre>VSI_ShellExecute("Procmon", "%temp%\VSI\Runtime\Lib\Procmon\Procmon.exe", "/Terminate")</pre>
With two simple commands in a workload we are able to collect a Process Monitor trace. For a full example I'm using the eDrawings workload created in a <a href="http://www.logitblog.com/login-vsi-workload-customization/" target="_blank">previous blog post</a>. Before we start using the application we start a Process Monitor trace. When we are finished with the application we stop the trace.
<pre>#WLProp:Version#1.0.1
#WLProp:Date#2013-12-12
#WLProp:Description#The Logitblog eDrawing Procmon Workload
##### Start segment Prepare
Segment("Workload", "Prepare")

# Skip eDrawing registration screen
VSI_RegWrite("Workload", "HKEY_CURRENT_USER\Software\eDrawings\e2014\General", "Activated", "REG_DWORD", "1")

# Random wait before starting workload
Workload_RandomIdle("Workload", 5, %VSI_RandomInitialWait%, "Waiting to start workload")

# Set mouse position
VSI_Mouse_Position("Workload")

##############################################################
##### Start segment 1 - eDrawing + Procmon               #####
##############################################################
Segment("Workload", 1)

# Start Procmon Trace
VSI_ShellExecute("Procmon", "%temp%\VSI\Runtime\Lib\Procmon\Procmon.exe", "/AcceptEula /Quiet /Minimized /BackingFile %systemdrive%\%computername%-Procmon.pml")

VSI_Random_File_Copy("eDraw1", "EASM", "%VSI_Userhome%\EASM1.easm")
App_Start("eDraw1", "", "%VSI_Userhome%\EASM1.easm", "Title", "eDrawings")
App_Focus("eDraw1", "Title", "eDrawings", "", "Maximize")
VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
App_Close("eDraw1", "Title", "eDrawings")

# Stop procmon trace
VSI_ShellExecute("Procmon", "%temp%\VSI\Runtime\Lib\Procmon\Procmon.exe", "/Terminate")</pre>
<h2>Conclusion</h2>
With two simple commands we can create a Process Monitor trace which we can use to troubleshoot a specific or more applications on multiple targets. This way you can use Login VSI to reproduce the exact same behavior in an application while Process Monitor captures all the information. It is an efficient way to minimize the trace size and capturing only the duration when the application is used.

If you have any comments or questions leave a message below.
