---
layout: post
title: 'Hands-on: MSIX'
authors: [ryan]
comments: true
categories: [MSIX]
tags: [Adobe Reader DC, App-V, Microsoft, MSIX, Packaging, Powershell]
image: assets/images/posts/2018-10-29-hands-on-msix/hands-on-msix-feature-image.png
---
The last couple of months there is more and more buzz around MSIX. With the Windows 10 1809 release, MSIX packages are fully supported and the MSIX packaging tool is out of preview. For a ICT-R research, I collected interesting results with the new MSIX format running in a VDI scenario but that will be published at ICT-R soon. In this blog post, I will share my hands-on experience with MSIX.

<!--more-->
<h2>What is MSIX?</h2>
MSIX is going to be the new format which can replace both the traditional MSI and setups as well as AppX which is the current Microsoft Store format. MSIX will inherit all the features of UWP and it also introduces more container security options and allows application customizations. Microsoft open sourced the entire project on <a href="https://github.com/Microsoft/msix-packaging" target="_blank" rel="noopener">GitHub</a>. This strategy will definitely help with the adoption of the new format. Now existing App-V packages can be converted to the new MSIX format. This allows organizations to convert App-V packages to the MSIX format and make them available from the Microsoft Store for Business. Eventually, MSIX will be the new industry standard and traditional installation with MSI and setups will disappear. Now, this is not done overnight and will take a lot of time. What do you think? More information about MSIX can be found <a href="https://docs.microsoft.com/en-us/windows/msix/overview" target="_blank" rel="noopener">here</a>.

<iframe width="800" height="461" src="https://www.youtube.com/embed/phrD081sMWc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<h2>Getting started with MSIX</h2>
Usually, in my hands-on posts, I provide a step for step guide of the product. In case of MSIX my friend Rory Monaghan has created an excellent post on how to create a package. So, therefore, I'm not going to cover the creation process of making your first package. Instead, I going to share some experiences I had during this process.

So for the complete guide please visit Rory his <a href="https://www.rorymon.com/blog/how-to-create-an-msix-package-with-the-msix-packaging-tool/" target="_blank" rel="noopener">blog post</a> and I recommend to watch his video.

<iframe src="https://player.vimeo.com/video/284725554" width="800" height="461" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>

<h2>Singing your package</h2>
MSIX requires to sign your package with a certificate. Now during the packaging process, you will be asked to select your ".PFX" file and provide the password for the certificate. In this step, it is important to have a valid certificate and you provide the right credentials. When providing the incorrect credentials you are still able to continue and complete the process. At the end during the package creation step the tool will fail with a "Package creation error".

Within the debug log it will show it could not sign the package with the provided password. So please note, when providing the package information ensure the certificate is fully resolved. If this is not the case you probably provided incorrect credentials.

<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-package-cert-resolved.png"><img class="alignnone size-medium wp-image-4745" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-package-cert-resolved.png" alt="" width="300" height="226" /></a>
<h2>Save the package before editing</h2>
Once you're done installing the application and capturing all the changes you have the option to create and open the package in the package editor. When opening in the packaging editor I experienced an error and lost the package. When looking into the debug log the following error appeared:
<blockquote>[Error] Error Occurred: System.ArgumentNullException: Value cannot be null.</blockquote>
<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-debug-log-packaging-error.png"><img class="alignnone size-medium wp-image-4740" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-debug-log-packaging-error.png" alt="" width="300" height="141" /></a>

So based on this it is better to first fully create and save the MSIX package before editing.
<h2>Adding the package</h2>
Before adding the package you have to ensure at least the "Sideload apps" setting is enabled in the developer settings.

<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-sideload-apps.png"><img class="alignnone size-medium wp-image-4743" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-sideload-apps.png" alt="" width="300" height="244" /></a>

There are multiple ways to install the package on the system. For the current user, you can just add the MSIX package by just double clicking on the package itself. This will install the package only for the current user. Now I have to say this method does not always work. On my laptop, I was not able to install the package even when the MSIX packaging tool is installed.

An alternative way is to use PowerShell. This can be done using the following command:
<pre class="lang:ps decode:true">Add-AppXPackage -Path [path]</pre>
Please note, this way the package is added only for the current user. If you need the package available for all users, then you need to provision the package which can be done using this command:
<pre class="lang:ps decode:true">Add-AppxProvisionedPackage -PackagePath [path] -Online -SkipLicense</pre>
This way the package is added for every user on the machine.

<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-add-package.png"><img class="alignnone size-medium wp-image-4741" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-add-package.png" alt="" width="300" height="244" /></a>
<h2>Adobe Reader DC in MSIX format</h2>
Now for my test scenario, I intended to use an Adobe Reader DC MSIX package. The creation of the package itself worked as expected but once the package was added on the machine I experienced a strange behavior. Once the application is started everything looks normal, but after a couple seconds, an error message occurred stating it cannot find the executable. After dismissing the first error another will occur.

<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-error-two.png"><img class="alignnone size-medium wp-image-4739" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-error-two.png" alt="" width="300" height="229" /></a> <a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-error-one.png">   <img class="alignnone size-medium wp-image-4738" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-error-one.png" alt="" width="300" height="231" /> </a>

Looking into the package I was able to verify the executable was there, so I do not understand the error.

<a href="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-exe-vfs.png"><img class="alignnone size-medium wp-image-4746" src="{{site.baseurl}}/assets/images/posts/2018-10-29-hands-on-msix/msix-adobe-reader-dc-exe-vfs.png" alt="" width="300" height="110" /></a>

I took me too much time to troubleshoot this issue and decided to replace Adobe Reader DC with another PDF Reader. But I think the error has to do with the default associations. By default Adobe Reader DC wants to configure itself to the default PDF viewer when this is not configured. Of course, I tried to configure this within the package but the error message still occurs. With Adobe Reader DC in an App-V package, I did not experience this behavior. I'm curious if anyone else experiencing the same issue, please leave a comment below.

<h2>Conclusion</h2>
I fully understand why Microsoft is moving toward MSIX. The first attempt with the AppX package was not that successful as vendors are not fully committed to the new standard. By making it fully open source format and abilities to convert from an existing format like App-V I expect this will be a success. Now, will it replace App-V? I'm not sure. Currently, they are not clear about the future of App-V and there is no distribution infrastructure like App-V. So Microsoft, what is the plan with MSIX and App-V? Please share some insights.

Did you already try MSIX out? Share your insights in the comments below!

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/4iOcciwDzRw?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Nynne Schrøder</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener">Unsplash</a></span>
