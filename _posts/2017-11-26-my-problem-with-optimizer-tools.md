---
layout: post
title: My problem with optimizer tools
authors: [ryan]
comments: true
categories: [VDI]
tags: [VDI, Citrix Optimizer, VMware OSOT]
image: assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/my-problem-with-optimizer-tools-feaature-image.png
---
Applying optimizations within a VDI environment is nowadays the de facto standard. There are different tools available to help you apply optimizations according to best practices. These optimizer tools are useful but also have a downside. In this blog post, I will share my problem with these optimizer tools.

<!--more-->
<h1>The importance of optimizing</h1>
Before we start comparing different options it is import to understand the importance of optimizing a VDI environment. A default installation of an operating system contains roles and features which may not be used. These roles and features may look innocent but may consume resources in the background. This can result in a lower density of your VDI environment which increases the cost per desktop. Besides density, it may also affect overall user experience as applications become slower.

There are different components to optimize, like removing roles or features, disabling services and tasks or applying specific settings in the registry. But also think about antivirus solutions as they can heavily impact performance.

Collecting all these best practices is quite a search but luckily there are various tools available that contains these best practices.

<span style="color: #444444; font-family: Montserrat, Helvetica, Arial, sans-serif; font-size: 2rem;">Available optimizer tools: VMware vs Citrix</span>

The two most common tools available today are VMware OSOT and Citrix Optimizer. Both are free and contains default templates for various operating systems. These tools provide a user interface which you can enable or disable specific optimizations.

Of course, both tools provide a way to apply these optimizations automated so it can be integrated with a deployment.

For more information about the difference between both tools, I can recommend the following post by Chris Twiest, also known as Workspace Guru. He has written an excellent blog post comparing both VMware OSOT and Citrix Optimizer.

https://workspace-guru.com/2017/11/07/optimizers-vmware-vs-citrix/
<h2>Understanding what is going on</h2>
There is also a downside to all these optimizations as it may break applications or even worse, Windows itself. So checking all the checkboxes comes with a high risk. Therefore it is imported to understands the consequences and impact of the optimization changes.

When applying optimizations to a production environment you should know which registries, services, tasks etc are change. The tools mentioned above provide insight into the changes to be made on the operating system but how are these applied?

I have a problem with tools like VMware OSOT and Citrix Optimizer because it is not clear how the optimization is applied. It states in the user interface it is changing one registry key, but how can you be so sure?

Next to that these tools are biased because both tools are published by vendors which make them in control.

Don't get me wrong, both tools are great and I also used them in a lab environment. But in the end, I still prefer a PowerShell code which makes it clear how these optimizations are applied.
<h2>Alternative solution: We Optimize Tool</h2>
Because of my opinion about these optimizer tools, I decided to start a little project to named We Optimize Tool. The goal is to create a tool just like VMware OSOT and Citrix Optimizer with a couple big differences.
<ol>
 	<li>Not biased, it is vendor independent;</li>
 	<li>Web-based configuration;</li>
 	<li>Generating PowerShell code.</li>
</ol>
The end-goal is to make this a community driven tool where everybody can contribute to various templates.

Let me show how the tool currently looks like!
<blockquote><strong>Please note:</strong> this project is still work in progress. The look and feel may change overrtime.</blockquote>
Just like the other tools the main goals is to provide various templates. When visiting the site you will see them presented in a card style.

<a href="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-main.png"><img class="alignnone size-medium wp-image-4433" src="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-main.png" alt="" width="300" height="242" /></a>

When opening a card all the optimizations are shown. Using the simple checkboxes you can include or exclude various items. When your done selecting the desired items you can click the code button in the bottom right corner to generate the PowerShell code. And of course, there is also a dark theme!

<a href="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-items.png"><img class="alignnone size-medium wp-image-4436" src="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-items.png" alt="" width="300" height="242" /></a> <a href="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-code.png"><img class="alignnone size-medium wp-image-4434" src="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-code.png" alt="" width="300" height="242" /></a> <a href="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-dark.png"><img class="alignnone size-medium wp-image-4435" src="{{site.baseurl}}/assets/images/posts/2017-11-26-my-problem-with-optimizer-tools/w0t-dark.png" alt="" width="300" height="242" /></a>

Using the generate PowerShell code makes it understandable what is changed within the operating system without using a tool. So even if you are not the best PowerShell guy, this still makes you in control!

We Optimize Tool is currently available at <a href="https://w0t.logitblog.com" target="_blank" rel="noopener">w0t.logitblog.com</a>! Please note, it is work in progress.

<h2>Conclusion</h2>
Applying optimization in a VDI environment is common to get the best user experience. There are various tools available which can help you apply optimizations according to best practices. Be careful with applying these optimizations because it may break applications or even Windows. My preference goes to just PowerShell code so I'm in control of what going to be changed and therefore I created a public tool named We Optimize tool (W0T).

If you have any feedback please leave them in the comments below.

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/IClZBVw5W5A?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Todd Quackenbush</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>