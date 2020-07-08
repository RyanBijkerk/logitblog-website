---
layout: post
title: 'Hands-on: Windows Containers powered by Docker'
authors: [ryan]
comments: true
categories: [Containers]
categories: [Containers, Docker, Windows Server 2016]
image: assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/hands-on-windows-containers-powered-by-docker-feature-image.png
---
Efficient use of computing resources can reduce a cost of hardware and maintenance within organizations. Back in the day virtualization made a big difference but is the containers technology going to make the same difference in the market? Microsoft is adopting the container technology and this blog post is going to take a closer look at Windows Containers which is powered by Docker.
<h2>Introduction</h2>
Microsoft is partnering with Docker to enable creation, management, and deployment both Windows Server and Linux-based containers from the Windows platform. Docker allows you to package applications with all the dependencies into a standardized unit. Containers are very popular in the software development but it has a lot of potential in the IT operations within organizations. Containers have a resource isolation and allocation benefits like virtual machines but a different architectural approach allows them to be much more portable and efficient.

<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/what-is-docker-diagram.png"><img class=" wp-image-3653 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/what-is-docker-diagram.png" alt="what-is-docker-diagram" width="193" height="211" /></a>            <a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/what-is-vm-diagram.png"><img class="wp-image-3654 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/what-is-vm-diagram.png" alt="what-is-vm-diagram" width="227" height="164" /></a>

Source: <a href="https://www.docker.com/what-docker">Docker.com</a>

Within the technical previews of Windows Server 2016, Docker is available.
<h2>Installation of Windows Containers</h2>
During my hands-on experience, I followed multiple guides and can highly recommend the <a href="https://msdn.microsoft.com/en-us/virtualization/windowscontainers">Windows Container Documentation </a>site from Microsoft. Please note an internet connection on the server is required.

The first step is to enable the Windows Containers feature. This can be done by using a PowerShell command or through the Server Manager. In this example, the PowerShell command is used.
<pre class="lang:ps decode:true">Install-WindowsFeature containers</pre>
After the feature is enabled the server needs to be rebooted.

In order to manage the Container Images, the PowerShell module needs to be installed.
<pre class="lang:ps decode:true ">Install-PackageProvider ContainerImage -Force</pre>
After this, you are able to list the available Container Images.
<pre class="lang:ps decode:true ">Find-ContainerImage</pre>
These Container Images are queried from the internet and must be installed.
<pre class="lang:ps decode:true ">Install-ContainerImage -Name WindowsServerCore</pre>
This can take a while depending on your internet connection because the WIM file is downloaded.

The last part of the installation will be installing the Docker service and engine. Microsoft provides a script that downloads all the required files and installs Docker on the server.
<pre class="lang:ps decode:true ">Invoke-WebRequest https://aka.ms/tp5/Update-Container-Host -OutFile update-containerhost.ps1</pre>
Execute the script in order to install Docker.
<pre class="lang:ps decode:true">.\update-containerhost.ps1</pre>
When the script is complete the installation can be verified by running the command "docker version". This will provide a response with the details of the Docker client and server.
<pre class="lang:ps decode:true ">docker version</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-version.png"><img class="alignnone size-large wp-image-3660" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-version.png" alt="docker-version" width="584" height="153" /></a>
<h2>Creating and running a Windows Container</h2>
Now Docker is installed you are ready to create and run containers. During the installation, a image has been installed and this can be verified by Docker.
<pre class="lang:ps decode:true">docker images</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images.png"><img class="alignnone size-large wp-image-3661" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images.png" alt="docker-images" width="584" height="73" /></a>

As the result shows there is one image available with a specific tag. When running a container the image cannot be found because by default the tag latest is used. Therefore it is recommended to create a latest tag for the "Windows Server Core" image.
<pre class="lang:ps decode:true">docker tag [image id] windowsservercore:latest</pre>
Now there are two images available with one of them tagged as the latest.
<pre class="lang:ps decode:true">docker images</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images-tag.png"><img class="alignnone size-large wp-image-3662" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images-tag.png" alt="docker-images-tag" width="584" height="115" /></a>

Now we are going to create a container running IIS with a custom landing page. First, a container needs to be started.
<pre class="lang:ps decode:true">docker run --name iisbase -it windowsservercore cmd.exe</pre>
The command prompt will be shown in the same window and now the feature IIS can be enabled.
<pre class="lang:ps decode:true">powershell -Command "&amp; {Install-WindowsFeature web-server}"</pre>
After IIS is enabled we can remove the default IIS start page and add a basic page with a custom text.
<pre class="lang:ps decode:true">del "C:\inetpub\wwwroot\iisstart.htm"

echo "Hello World From a Windows Server Container" &gt; C:\inetpub\wwwroot\index.html</pre>
After this is done the container can be closed by just the exit command.

Now the base of the IIS container is created and this can be committed as an image so it can be re-used.
<pre class="lang:ps decode:true">docker commit iisbase windowsservercoreiis</pre>
When the commit is completed the image is listed in the overview.
<pre class="lang:ps decode:true">docker images</pre>
<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images-with-iis.png"><img class="alignnone wp-image-3659 size-large" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-images-with-iis.png" alt="docker-images-with-iis" width="584" height="111" /></a>

A container can be started based on the IIS image that we just created. Because the container needs to be accessible from the outside the port needs to be specified.
<pre class="lang:ps decode:true ">docker run --name iisdemo -it -d -p 80:80 windowsservercoreiis</pre>
Now the website is running hosted within a Windows container and accessible by port 80. Please note in this example I'm browsing the website from another machine.

<a href="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-run-iis-website.png"><img class="alignnone size-medium wp-image-3658" src="{{site.baseurl}}/assets/images/posts/2016-05-10-hands-windows-containers-powered-docker/docker-run-iis-website.png" alt="docker-run-iis-website" width="300" height="74" /></a>

In order to stop the container, the following command can be used.
<pre class="lang:ps decode:true ">docker stop iisdemo</pre>
<h2>Conclusion</h2>
My first attempt was not going how it should be and I ran into a lot of issues. In my second attempt, I was able to get Windows containers up and running quite quickly. While this introduction is very basic it provides a good understanding how Window container works. Containers enable to isolate applications and make optimal use of the computing resources. I hope this hands-on blog post is useful and if you have feedback or questions please leave a comment.
