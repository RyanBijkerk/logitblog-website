---
layout: post
title: Visual Studio Online build version increment
authors: [ryan]
comments: true
categories: [Development]
tags: [Development, Powershell, Visual Studio Online]
image: assets/images/posts/2016-01-14-visual-studio-online-build-version-increment/visual-studio-online-build-version-increment-feature-image.png
---
I'm currently using Visual Studio Online for my tooling and I was searching for a way to increment the build version for my solution. After searching the internet I found the following <a href="http://incyclesoftware.com/2015/06/vnext-build-awesomeness-managing-version-numbers/" target="_blank">blog post </a>which I applied, but for some reason it was not working as expected. I'm not using a default version format and this was causing the build to fail. In this blog post I'm going to share how I fixed it.
<h2>Important note</h2>
Before I start, I want to point out the best practices regarding .NET assembly versioning which can be found <a href="http://blogs.msdn.com/b/jjameson/archive/2009/04/03/best-practices-for-net-assembly-versioning.aspx" target="_blank">here</a>.
<h2>The problem and solution</h2>
I created the variables as the <a href="http://incyclesoftware.com/2015/06/vnext-build-awesomeness-managing-version-numbers/" target="_blank">blog post</a> described and set the build format number "$(BuildDefinitionName)_$(MajorVersion).$(MinorVersion)$(Rev:.r)" which is like 1.0.1.

Before running a build I have added the "ApplyVersionToAssemblies.ps1" script and included the PowerShell script in a build step. Next step is starting a build but it failed with the following PowerShell error:
<blockquote>Could not find version number data in BUILD_BUILDNUMBER.</blockquote>
Looking into the script this the error is caused by the the RegEx check which is for finding the version number from the build format property. Because I'm not using a standard formatting number the script is expecting a build number like 0.0.0.0. In order to fixes the error we need to modify the "ApplyVersionToAssemblies.ps1". On line 16 in the regular expression is defined and I modified to the following:
<pre class="lang:ps decode:true "># Regular expression pattern to find the version in the build number 
# and then apply it to the assemblies
$VersionRegex = "\d+\.\d+\.\d+"</pre>
After the modification the build was successful and the version was applied correctly.
<h2>Conclusion</h2>
With some small modification in the "ApplyVersionToAssemblies.ps1" PowerShell script it is possible to modify the build format. I took me some time to figure it out and get it working so I hope it helps a bit.

Sources:

<a href="https://msdn.microsoft.com/Library/vs/alm/Build/scripts/index" target="_blank">https://msdn.microsoft.com/Library/vs/alm/Build/scripts/index</a>

<a href="http://blogs.msdn.com/b/jjameson/archive/2009/04/03/best-practices-for-net-assembly-versioning.aspx" target="_blank">http://blogs.msdn.com/b/jjameson/archive/2009/04/03/best-practices-for-net-assembly-versioning.aspx</a>

<a href="http://incyclesoftware.com/2015/06/vnext-build-awesomeness-managing-version-numbers/" target="_blank">http://incyclesoftware.com/2015/06/vnext-build-awesomeness-managing-version-numbers/</a>
