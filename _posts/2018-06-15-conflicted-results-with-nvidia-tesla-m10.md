---
layout: post
title: Conflicted results with NVIDIA Tesla M10
authors: [ryan]
comments: true
categories: [NVIDIA]
tags: [Citrix, GRID, Login VSI, Nvidia, NVIDIA, Remote Display Analyzer, Tesla, vGPU]
image: assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/conflicted-results-with-nvidia-tesla-m10-featrue-image.png
---
The use of an NVIDIA GPU in a centralized desktop environment is getting more common. More and more applications are using a GPU to render the graphics. Even applications like Microsoft Word and PowerPoint are using these GPU's. I'm currently researching the impact of an NVIDIA M10 on a Citrix XenDesktop environment with some conflicting results. In this blog post, I will share some details of the research.

<!--more-->
<h2>The need for a GPU</h2>
Over time applications are more and more levering the advantage of the GPU. According to NVIDIA, these applications have doubled over the past 5 years.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/nvidia-gpu-apps-doubled.png"><img class="alignnone wp-image-4568 size-full" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/nvidia-gpu-apps-doubled.png" alt="" width="941" height="332"></a>

In a traditional VDI environment, this GPU related demand will be offloaded to the CPU. According to NVIDIA using a vGPU will result in 30% less CPU consumption.
<blockquote>With a GPU accelerated VDI environment, the addition of compute and graphics resources offload the CPU so that more users can be supported on a server.

Based on data from Lakeside and customer deployments, we’ve found that the addition of a GPU results in 30% less CPU consumption. Subsequently, Windows 10 VDI architects can increase server density (in CPU bound cases) by 30% through the inclusion of virtual GPU (vGPU).</blockquote>
Source: <a href="http://images.nvidia.com/content/grid/pdf/grid-win10-ebook.pdf" rel="noopener">nvidia.com</a>
<h2>The goal and context</h2>
Of course, it all starts with the right context. The goal of this research is to understand the impact of a GPU and if it is really required for a regular user running Windows 10 with Office 2016. The environment is delivered by Citrix XenDesktop 7.15 running Windows 10 x64 build 1709. It's all hosted on HP hardware running VMware ESX 6.5 combined with two NVIDIA Tesla M10 cards. Because of Windows 10 and Citrix XenDesktop, the minimal GPU profile is the 1B which has 1GB of memory with hardware encoding enabled. The maximum user density is 64 which is limited by the GPU. All the tests were executed on the same resolution, 1280x800.

For the load generation the product Login VSI is used running the default KnowlegdeWorker workload. This contains regular Office use, some PDF reading, internet browsing and video content. The workload is slightly modified where the PDF Printing is removed and the video content is updated to 720p videos. During the test runs, performance data is captured from the ESX host, launchers, and within the VM using Remote Display Analyzer.

As you may have seen (<a href="https://www.rdanalyzer.com/remote-display-analyzer-announcements/" target="_blank" rel="noopener">link</a>), I have joined the Remote Display Analyzer team to help develop this awesome tool. One of the requested features is capturing the data. In this test scenario, I'm using a development version of Remote Display Analyzer with the logging feature included. Keep an eye out on the <a href="https://www.rdanalyzer.com/" target="_blank" rel="noopener">official website</a> for this release!

To ensure there is enough launcher capacity the pool has increased to 14 launchers and all suited with 1B GPU profile running on a separate host. All these tests are automated and running 3 times and the results published in this blog post are an average of the 3 runs.
<h2>The overall impact from a host perspective</h2>
The NVIDIA solution sounds interesting even for the default VDI desktop. The lower CPU consumption allows buying a less core CPU which are in general cheaper. But as the title of this blog post suggests, the results gathered are conflicting with the statement of NVIDIA. In fact, there is the opposite way.

<table>
<tbody>
<tr>
<td colspan="1" rowspan="1" width="20"><strong>Color</strong></td>
<td colspan="1" rowspan="1" width="300"><strong>Test Name</strong></td>
<td colspan="1" rowspan="1" width="100"><strong>vCPU's</strong></td>
<td colspan="1" rowspan="1" width="100"><strong>vGPU</strong></td>
<td colspan="1" rowspan="1" width="100"><strong>Memory</strong></td>
</tr>
<tr>
<td colspan="1" rowspan="1" style="background-color: #bd7150;" width="20"></td>
<td colspan="1" rowspan="1" width="300">Clean without GPU and 2vCPU's</td>
<td colspan="1" rowspan="1" width="100">2</td>
<td colspan="1" rowspan="1" width="100">None</td>
<td colspan="1" rowspan="1" width="100">8GB</td>
</tr>
<tr>
<td colspan="1" rowspan="1" style="background-color: #374649;" width="20"></td>
<td colspan="1" rowspan="1" width="300">Clean without GPU and 4vCPU's</td>
<td colspan="1" rowspan="1" width="100">4</td>
<td colspan="1" rowspan="1" width="100">None</td>
<td colspan="1" rowspan="1" width="100">8GB</td>
</tr>
<tr>
<td colspan="1" rowspan="1" style="background-color: #6a9fb0;" width="20"></td>
<td colspan="1" rowspan="1" width="300">Clean with GPU and 2vCPU's</td>
<td colspan="1" rowspan="1" width="100">2</td>
<td colspan="1" rowspan="1" width="100">1B</td>
<td colspan="1" rowspan="1" width="100">8GB</td>
</tr>
</tbody>
</table>
The tests show there is an increase in hypervisor CPU utilization when having a vGPU. The overall utilization is 12% higher compared to the 2vCPU's without a vGPU scenario. Compared to the 4vCPU's without a vGPU scenario, the difference is 8%.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/overall-host-util.png"><img class="alignnone size-large wp-image-4626" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/overall-host-util.png" alt="" width="790" height="206"></a>

The difference between the scenario's when all the users are active is the same.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/active-users-host-util.png"><img class="alignnone size-large wp-image-4623" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/active-users-host-util.png" alt="" width="790" height="206"></a>
<h2>User experience with and without a GPU</h2>
Measuring the user experience is very difficult because it is always a personal opinion. What I find a great user experience may be a poor user experience for you.

From a Login VSI perspective, it is interesting to see the baseline of the GPU scenario is slightly higher with 36ms compared to the 2vCPU's without a GPU scenario. As expected the 4vCPU's has a better baseline compared to the 2vCPU's without a GPU. The Login VSI baseline is measured based on response times of various applications. As Windows 10 is aware of a GPU the visuals may affect the results in the GPU scenario.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/loginvsi-baseline.png"><img class="alignnone size-full wp-image-4624" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/loginvsi-baseline.png" alt="" width="760" height="200"></a>

Remote Display Analyzer captures multiple metrics and one of these is the frames per seconds (FPS). There are the number of frames sends over the protocol during the tests. I think it is a great metric but not really reliable. When there are no screen updates the FPS will be 1, and this can be confusing over time.

It is very interesting to see the FPS difference between the two scenarios is minimal. Overall the tests without a GPU and 2vCPU's have slightly less FPS of 0.6. A user wouldn't notice the difference.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/overall-fps.png"><img class="alignnone size-large wp-image-4625" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/overall-fps.png" alt="" width="790" height="190"></a>

In the workload, there is some video content played in the web browser. The results show the GPU scenario has 4 FPS more during the playback of a video.

<a href="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/video-content-fps.png"><img class="alignnone size-large wp-image-4627" src="{{site.baseurl}}/assets/images/posts/2018-06-15-conflicted-results-with-nvidia-tesla-m10/video-content-fps.png" alt="" width="790" height="176"></a>

As these scenarios are Office focused that include some videos there is no real difference between the scenarios. It may be interesting to see if there is a bigger difference when the resolution is higher like using multiple screens.
<h2>Outdated Login VSI workloads</h2>
Based on the results we can conclude there is no benefit adding a vGPU running the Login VSI KnowledgeWorker workload. But I have to be honest, the Login VSI workloads are a bit outdated. The current Login VSI workloads where introduced at the beginning of 2014. It would make sense if the workloads are updated using a modern browser (Edge, Chrome or Firefox) and by default higher resolution video content which is by default on YouTube for example. I also would love to see an updated library that includes some modern websites. Multitasking would be realistic as you can expect that from a knowledge worker. It's still one of the best products out there for VDI benchmarking but I think its time to get an updated VSI version which reflects the current world. Adding these changes will probably benefit a vGPU more.

Of course, I provided the feedback to Login VSI and received an official statement:

<blockquote>Our testing methodology and workloads are still the industry standard and is widely used.</blockquote>
Unfortunately, NVIDIA did not provide any public feedback on the results (yet).

## Conclusion

Based on the data it is clear adding a GPU requires more CPU as the CPU host utilization is higher. This means the additional cost for the NVIDIA hardware and license you also need more CPU capacity. These are really conflicting results compared to the marketing content from NVIDIA. The Login VSI workload is a bit outdated which reflects on the results. But based on this data, users that are like the Login VSI Knowledge Worker, which is not likely, do not require a vGPU. This will change when using intense GPU applications, in that case, it is recommended to use the NVIDIA solution. What is your experience with NVIDIA in a regular Office / Knowledge Worker scenario? I hope my insights are valuable and if you have any comments or questions you can leave them below.

Credits to <a href="https://www.linkedin.com/in/arjanvroege/" target="_blank" rel="noopener">Arjan Vroege</a> (<a href="https://www.vroege.biz/" target="_blank" rel="noopener">blog</a>), as he helped out to collect all these results.

<span style="text-decoration-line:underline; font-size:0.9375rem;">Photo by </span><a style="background-color: #ffffff; font-size: 0.9375rem;" href="https://unsplash.com/photos/b-9PBmDGOHc?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Saffu</a><span style="text-decoration-line:underline; font-size:0.9375rem;"> on </span><a style="background-color: #ffffff; font-size: 0.9375rem;" href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a>
