---
layout: post
title: Windows Container download issues
authors: [ryan]
comments: true
categories: [Containers]
tags: [Docker, Containers, Windows Server 2016]
image: assets/images/posts/2016-06-06-downloading-issues-windows-container/windows-container-download-issues-feature-image.png
---
As mentioned in my previous <a href="http://www.logitblog.com/hands-windows-containers-powered-docker/" target="_blank">blog post</a> my first attempt installing and configuring Windows Containers was not very successful. I experienced download issues of various Windows Container components and received messages like "Unable to download the list of available providers" or "Bits Transfers failed". This blog post will explain more about the root cause and how to solve these issue so you can start using Windows Containers.
<h2>My first experience with Windows Container</h2>
After enabling the feature a package provider needs to be installed. When running the command "Install-PackageProvider ContainerImage -Force" the following message can be displayed.
<pre class="lang:ps decode:true ">WARNING: Unable to download the list of available providers. Check your internet connections.</pre>
<pre class="lang:ps decode:true">Install-PackageProvider : No match was found for the specified search criteria for the provider 'ContainerImage'. 
The package provider requires 'PackageManagement' and 'Provider' tags. Please check if the specified package has the tags.</pre>
So as mentioned I checked my internet connection and I could ping bing.com without any problem.

<a href="{{site.baseurl}}/assets/images/posts/2016-06-06-downloading-issues-windows-container/check-your-internet-connection.jpg"><img class="alignnone size-medium wp-image-3680" src="{{site.baseurl}}/assets/images/posts/2016-06-06-downloading-issues-windows-container/check-your-internet-connection.jpg" alt="check-your-internet-connection" width="300" height="103" /></a>

After some searching on the internet I found the following <a href="http://stackoverflow.com/questions/11696944/powershell-v3-invoke-webrequest-https-error#answers-header" target="_blank">Stack Overflow article</a>. With the following lines I was able to run the Install-PackageProvider command.
<pre class="lang:ps decode:true ">add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
            ServicePoint srvPoint, X509Certificate certificate,
            WebRequest request, int certificateProblem) {
            return true;
        }
    }
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy</pre>
Following the guide "Find-ContainerImage" is up next but also this command is not running as expected. The following message is displayed.
<pre class="lang:ps decode:true ">WARNING: Cannot bind the argument to parameter 'fastPackageReference' because it is an empty string.
WARNING: The property 'version' cannot be found on this object. Verify that the property exists.
WARNING: The property 'Name" cannot be found on this object. Verify that the property exists.
WARNING: Cannot find path 'C:\Users\[username]\AppData\Local\ContainerImage\ContainerImageGallery_ContainerImageSearchIndex.txt' because it does not exists.
WARNING: Save-HTTPItem: Bits Transfer failed. Job State: BG_JOB_STATE_ERROR ExitCode = 0</pre>
Now what is not captured in this message is the download attempt of the txt file and with a screenshot I was able get the URL which is the <a href="https://pshctnoncdn.blob.core.windows.net/pshctcontainer/SearchContainerImages.txt" target="_blank">SearchContainerImages.txt</a>.

I decided to download the required wim file and installed it by using "Install-ContainerOSImage -WimPath [path]".

One of the last steps to get Docker up and running is to install the Docker client. Now Microsoft delivers a script but again this was not working so by manually downloading the script and pointing all the download references to a local copy of the binaries fixed it. By doing this I was able to verify the version by using the command "docker version". A image should be installed and this can be verified by the command "docker images".

Now Docker is running you should be able to run the container by the following command.
<pre class="lang:ps decode:true">docker run --name iisbase -it windowsservercore cmd</pre>
I received the following return message from Docker.
<pre class="lang:ps decode:true">Unable to fund image 'windowsserver:latest' locally
Pulling repository docker.io/libary/winwosservercore
Error while pulling image: get https://index.docker.io/v1/repositories/library/windowsservercore/images: x509: certificate signed by unknown authority.</pre>
This message is related to the images which is not marked as latest. Because it is not tagged as latest Docker will reach the internet to pull the latest version of this image. In order to resolve this you should tag you image as latest with the following command.
<pre class="lang:ps decode:true">docker tag windowsservercore:[version] windowsservercore:latest</pre>
After this you should be able to run a container.
<h2>The root cause of these downloading issues</h2>
When experiencing these downloading issues you can eventually get it up and running but it is a real hassle. When paying attention to the code part of Stack Overflow and the message "x509: certificate signed by unknown authority." it should trigger you there is something wrong with the certificates. Files can contain a certificate, this is common when shipping products and all the binaries contain a signature of the company. This also applies to secure websites. When downloading this from the internet it will automatically verify these certificates. If the certificate cannot be verified you will experience these kind of errors. I can happen a policy named "Turn off Automatic Root Certificates Update" is configured which is not updating these root certificated. This will result the certificates cannot be verified.
<h2>Conclusion</h2>
All these errors and messages are cause by certificates and you have to make sure the user is are allowed to verify these certificates. Check the applied group policies and specifically the "Turn off Automatic Root Certificates Update".

I hope this blog post is helpful and if you have a question please feel free to contact me.
