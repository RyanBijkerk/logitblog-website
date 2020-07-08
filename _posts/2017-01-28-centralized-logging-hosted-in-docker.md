---
layout: post
title: Centralized logging hosted in Docker
authors: [ryan]
comments: true
categories: [Containers]
tags: [Containers, Docker, Seq]
image: assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/centralized-logging-hosted-in-docker-feature-image.png
---
Proper logging helps to understand a problem with your software, especially during the development of a new product. I'm currently working on a new project where multiple microservices are hosted in separate Docker containers. In this scenario, centralized logging really helps to validate that everything goes as expected. In this blog post, I want to share how to run a logging solution named Seq in a Docker container.

<strong>Please note:</strong> this blog post will not cover how to integrate logging into your solution. For more information please take a look at the Seq documentation <a href="http://docs.getseq.net/docs" target="_blank">here</a>.
<h2>What is Seq?</h2>
Seq is a solution that helps you to centralize and structure logging of your solutions.
<blockquote>Modern structured logging bridges the gap between human-friendly text logs, and machine-readable formats like JSON. Using event data from libraries such as Serilog, ASP.NET Core, and Node.js, Seq makes centralized logs easy to read, and easy to filter and correlate, without fragile log parsing.

&nbsp;

Seq collects data over HTTP, while your applications use the best available structured logging APIs for your platform. The simple Windows installer, and plug-ins from NuGet or NPM, make getting started quick and friction-free.</blockquote>
For more information please visit the Seq<a href="https://getseq.net/" target="_blank"> website</a>.
<h2>Creating the centralized logging container</h2>
The installation of Seq is done by a 64 bit MSI based installer. You can download the latest installer from the Seq <a href="https://getseq.net/Download" target="_blank">website</a>. Because Seq is a Windows based solution and is installed with MSIEXEC, the Windows Server Core image is required. Create a Dockerfile and define the Windows Server Core image with the following line.
<pre class="lang:default decode:true">FROM microsoft/windowsservercore</pre>
Next step is to copy the Seq installer in the container. In my case, the Seq MSI is inside the same folder as the Dockerfile.
<pre class="lang:default decode:true">COPY . /app/

WORKDIR /app</pre>
Start the installation with MSIEXEC and the default parameters. In case something goes wrong we could use the installation logging.
<pre class="lang:default decode:true">RUN ["msiexec", "/i", "Seq-3.4.18.msi", "/quiet", "/norestart", "/log", "install.log"]</pre>
Seq is running as a service and once Seq is installed the registration still needs to take place. This can be done using the SC command. Please note that the start option needs to be on "auto" otherwise the service won't start.
<pre class="lang:default decode:true">RUN SC create seq binPath="C:\Program Files\seq\seq.exe run --storage=C:\app\seq" start=auto</pre>
Because there is no output, I've added a little PowerShell loop that will output the process usage to prevent the container from stopping.
<pre class="lang:default decode:true">CMD powershell.exe -command "&amp; {while ($true) {Get-Process -Name 'seq'; Start-Sleep 30}}"</pre>
You can download the full Dockerfile <a href="http://www.logitblog.com/downloads/dockerfile.txt" target="_blank">here</a>.

Now the Dockerfile is complete the container can be build by running the default build command.
<pre class="lang:ps decode:true">docker build . -t seq:latest
</pre>
Once the Docker image had been created you can list all images using "docker images".

<a href="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/docker-images.png"><img class="alignnone size-medium wp-image-4183" src="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/docker-images.png" alt="" width="300" height="130" /></a>

Now the image has been built you can run it with the following command. <strong>Please note:</strong> you need to map port 5341 to make it accessible from the outside.
<pre class="lang:ps decode:true">docker run -d -p 5341:5341 seq</pre>
Once the container is up and running you can reach it on the mapped port. This way you should be able to collect logs from different processes during development.

<strong>Please note: </strong>When running Docker on the same machine as your development environment you cannot access the container by hostname. You'll need to use the IP address instead. To get the correct IP address use the "docker inspect [container id]" command.
<h2>Composing Seq with other Docker containers</h2>
Once your solutions are also running within a container you'll need to link the individual containers to enable internal interaction. This can be done using a docker-compose file as shown below.
<pre class="lang:yaml decode:true">version: '2'
services:

    seq:
     image: seq
     ports:
      - 5341:5341
</pre>
In order to make communication possible, you need to link the microservices to the Seq container. This can be done by using the link option within the docker-compose file.
<pre class="lang:default decode:true">    ms1:
     image: microservices1
     ports:
      - 5000:5000
     links:
      - seq

    ms2:
     image: microservices2
     ports:
      - 5001:5001
     links:
      - seq</pre>
You can download the full docker-compose file <a href="http://www.logitblog.com/downloads/docker-compose.yml.txt" target="_blank">here</a>.

Now within the ms1 and ms2 container, Seq can be resolved by using the hostname seq. So within your solution, the report URL should be http://seq:5341.

In order to start all containers use the following command.
<pre class="lang:default decode:true">docker-compose up -d</pre>
<a href="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/docker-compose.png"><img class="alignnone size-medium wp-image-4181" src="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/docker-compose.png" alt="" width="300" height="74" /></a>

Once the containers are up you will notice that the microservices are logging to Seq.

<a href="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/seq-dashboard.png"><img class="alignnone size-medium wp-image-4180" src="{{site.baseurl}}/assets/images/posts/2017-01-28-centralized-logging-hosted-in-docker/seq-dashboard.png" alt="" width="300" height="219" /></a>
<h2>Conclusion</h2>
Logging is very important, especially when you need to troubleshoot an issue. Using a solution like Seq can really help with centralizing all logging of multiple solutions and structuring the logs. When using Docker you can run Seq within a Windows based container so other containers can directly log to Seq. With the docker-compose file, containers can link to each other and everything will be logged. As this solution is currently very useful for my colleague and me and I hope also for you. If you have any comments or questions please leave them below.

&nbsp;

&nbsp;
