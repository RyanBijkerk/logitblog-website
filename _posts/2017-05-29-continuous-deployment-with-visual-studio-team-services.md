---
layout: post
title: Continuous deployment with Visual Studio Team Services
date: 2017-05-29 17:47
authors: [ryan]
comments: true
categories: [Development]
tags: [Agile, Continuous Deployment, Development, Team Foundation Server, Tools, Visual Studio Team Services]
image: assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/continuous-deployment-with-visual-studio-online-feature-image.png
---
In many organizations releasing a new version of a product is a hassle. Many actions and people are required to get this new version online. By using the continuous deployment techniques you can automate this entire flow. In this blog post I will show how I organize the continuous deployment of my tools.
<h2>What is the benefit of continuous deployment?</h2>
When creating software, feedback is extremely important to improve. In order to get feedback from customers, you need to make sure your tool or product is available. With an Agile development strategy, it's common to have short increments called sprints. At the end of each sprint, you should have a potentially shippable product. The goal is to deliver the added value quickly and gather customer feedback.
<h2>How to start with continuous deployment?</h2>
It is important to understand the flow and the requirements to deploy your software. Make sure you fully understand what needs to be done. As described in the introduction I'll be using my tool as an example. Now, this may be a simple example but I hope it provides a generic idea how to approach this strategy.

Releasing a new version of my tool takes a few steps. When I have a successful build in Visual Studio Team Services. I download the artifacts, extract the zip and remove the unused debug files. Once the unused files are removed I zip the tool again and upload it to my blog. On the tool page, I update MD5 hash and the link to the latest version.

To automate this process I have enabled "Continuous Integration" on my release build which is linked to my master branch. Once I have finished my work I can merge my changes into the master branch and this will trigger the build automatically.

<a href="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-build-trigger.png"><img class="alignnone wp-image-4298 size-full" src="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-build-trigger.png" alt="" width="968" height="715" /></a>

As mentioned I need to make sure I only provide the required files for download. Within my release build, I have a copy task with a contents filter. In this example, I only need the executable and required text file. Now the artifact provided by the build includes the correct files.

<a href="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-copy-files-task.png"><img class="alignnone wp-image-4299 size-large" src="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-copy-files-task.png" alt="" width="790" height="310" /></a>

Currently, I have two release definitions. One for the release build which I call the stable release and another for my develop build which is the development release. In both definitions, I made sure the triggers are enabled based on the correct build.

<a href="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-trigger.png"><img class="alignnone wp-image-4301 size-large" src="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-trigger.png" alt="" width="790" height="415" /></a>

In the release definitions, I have two actions. First I'm creating a zip file based on the artifact from the specific build. Once the zip is created I can upload the file to my blog using FTP. In both releases, the zip is uploaded to the directory based on the branch name and specific version number. This way I have a clear distinction between a stable and development release.

<a href="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-zip.png"><img class="alignnone wp-image-4302 size-large" src="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-zip.png" alt="" width="790" height="281" /></a>

<a href="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-ftp.png"><img class="alignnone wp-image-4300 size-large" src="{{site.baseurl}}/assets/images/posts/2017-05-29-continuous-deployment-with-visual-studio-team-services/ppd-release-ftp.png" alt="" width="790" height="381" /></a>

This is a simple example but the possibilities are endless with all the tooling and options in Visual Studio Team Services.

<h2>Dynamic page with the latest build</h2>
Once the tool is uploaded to the FTP I need to update the download links to the latest release. I don't want to do this every time so therefore I have added some PHP code on my page to get the latest stable and development build. Because my blog is hosted in WordPress I'm using the <a href="https://wordpress.org/plugins/insert-php/" target="_blank" rel="noopener noreferrer">insert php plugin</a>.
<pre class="lang:php decode:true">[insert_php]
$path = getcwd();
$baseUrl = $_SERVER['SERVER_NAME'];

$developDir = "$path\\downloads\\ppd\\develop\\";
$masterDir = "$path\\downloads\\ppd\\master\\";

$developFiles = array_diff(scandir($developDir, SCANDIR_SORT_DESCENDING), array('..', '.'));
$developLast = $developFiles[0];
$developLink = "$baseUrl/downloads/ppd/develop/$developLast/PPD.zip";
$developMd5 = md5("$developDir\\$developLast\\PPD.zip");

$masterFiles = array_diff(scandir($masterDir, SCANDIR_SORT_DESCENDING), array('..', '.'));
$masterLast = $masterFiles[0];
$masterLink = "$baseUrl/downloads/ppd/master/$masterLast/PPD.zip";
$masterMd5 = md5("$masterDir\\$masterLast\\PPD.zip");

</pre>
Within the page, I can use the variables to provide the latest name, link and MD5 hash.
<pre class="lang:php decode:true">[insert_php]echo($developLast);[/insert_php]
[insert_php]echo($developLink);[/insert_php]
[insert_php]echo($developMd5);[/insert_php]
</pre>
This way when a new build has uploaded the page is automatically up to date. The only thing I have to maintain is the documentation.

<h2>Conclusion</h2>
Continuous deployment helps to quickly release new versions of your software so you can gather feedback. Using Visual Studio Team Services you have endless possibilities to deploy your software in any way. In my example, this is a simple creation of a zip file and upload to an FTP. By using a dynamic page, the latest builds are directly available for download. This way I only have to maintain updates to the documentation. I hope this blog post was useful and if you have any comments or questions please leave them below.
