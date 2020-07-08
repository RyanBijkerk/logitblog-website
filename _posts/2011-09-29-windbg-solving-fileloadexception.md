---
layout: post
title: 'WinDBG: Solving FileLoadException'
authors: [dennis]
comments: true
categories: [Troubleshooting]
tags: [FileLoadException, System.IO.FileLoadException, Troubleshooting, WinDBG]
image: assets/images/posts/2011-09-29-windbg-solving-fileloadexception/windbg-solving-fileloadexception-feature-image.png
---
Often an application will give you a generic error message like a FileLoadException when something goes wrong. Often you can fix the problem if you know what exactly went wrong. I've come across a dotNet program that gave me a generic error. While investigating the error with WinDBG I came across a more descriptive message that enabled me to fix the problem. In this post I describe the steps I took to diagnose the problem and solve it.

<strong>Please note:</strong> this post describes a specific problem and the exact method may not work for every application but the general method should apply to most dotNet applications.

An application is crashing during startup with the following message:

<img class="alignnone size-full wp-image-906" src="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/app-crash.png" alt="" width="366" height="152" />

The message itself isn’t very useful to troubleshoot the crash. The Windows eventlog is a little more helpful with the following error:

<img class="alignnone size-full wp-image-907" src="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/eventlog.png" alt="" width="632" height="440" />

While the logged error does point in the right direction it doesn’t show what file failed to load or why the file  failed to load. The application itself does not log what file failed to load or why the file failed to load either. A <a href="http://technet.microsoft.com/en-us/sysinternals/bb896645">ProcMon</a> log doesn’t reveal any access denied or file not found issues. More specific exception information can be obtained using WinDBG, part of the <a href="http://msdn.microsoft.com/en-us/windows/hardware/gg463009" target="_blank">Debugging Tools for Windows</a>.
<h2>Initial configuration</h2>
Configure the symbol path to point to the Microsoft symbol server. To do this either go to the File menu &gt; Symbol File Path and configure the path as follows:
<blockquote><em>SRV*D:\localsymbols*http://msdl.microsoft.com/download/symbols</em></blockquote>
This tells WinDBG to retrieve the required symbols from http://msdl.microsoft.com/download/symbols and put them in D:\localsymbols.

There are several alternative ways to configure symbols, including downloading the symbols from Microsoft as a separate package. Downloading the symbols as a separate package can be useful when your debugging system doesn’t have internet access.
More information about setting the symbol path can be found <a href="http://support.microsoft.com/kb/311503" target="_blank">here</a>.
<h2>The actual debugging</h2>
Go to File &gt; Open Executable and select the executable you want to debug. The application will start loading several assemblies and hit its first breakpoint.

<img class="alignnone size-full wp-image-904" src="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/initial-breakpoint.png" alt="" width="751" height="502" />

This breakpoint is caused by attaching the debugger to the executable and can be ignored. Continue debugging by pressing F5.
The next exception that stops the program is a second chance exception. This means the application had the chance to handle the exception but didn’t, more information about first and second chance exceptions can be found <a href="http://support.microsoft.com/kb/105675">here</a>.
The exception is a CLR (Common Language Runtime, part of the .NET framework) exception. By default WinDBG doesn’t know how to handle CLR exceptions. To make WinDBG understand CLR exceptions load a helper DLL clr.dll. Use the following command to load the dll:
<blockquote><em>.loadby sos clr</em></blockquote>
<em><a href="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/loadby-sos-clr.png"><img class="alignnone size-medium wp-image-905" src="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/loadby-sos-clr.png" alt="" width="300" height="227" /></a>
</em>

Loading clr.dll enables a few useful functions like !PrintException (or !pe for ease of typing). The !pe command shows the most recent exception.

<a href="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/pe.png"><img class="alignnone size-full wp-image-908" src="{{site.baseurl}}/assets/images/posts/2011-09-29-windbg-solving-fileloadexception/pe.png" alt="" width="1015" height="132" /></a>

The actual message that comes with the exception clearly shows the cause of the problem. Looking the message up on Google points me in the direction of <a href="http://stackoverflow.com/questions/1604663/what-does-uselegacyv2runtimeactivationpolicy-do-in-the-net-4-config">this page</a>.

As mentioned on the above page adding the useLegacyV2RuntimeActivationPolicy attribute to the startup element of the applications config file solved the issue. Below is a sample config file with the attribute added.

&nbsp;
<blockquote><em>&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;configuration&gt;
<span style="color: #ff0000;"><strong>&lt;startup useLegacyV2RuntimeActivationPolicy="true"&gt;</strong></span>
&lt;supportedRuntime version="v4.0" /&gt;
&lt;/startup&gt;</em></blockquote>
I’ve used <a href="http://ig2600.blogspot.com/2009/07/finding-clr-exceptions-with-visual.html">this</a> blogpost to help with the CLR debugging.
