---
layout: post
hidden: false
title: The 1902 release of Remote Display Analyzer (RDA)
authors: [ryan]
comments: true
categories: [RDA]
tags: [Citrix, Microsoft, RDA, Remote Display Analyzer, Tools, VMware]
image: assets/images/posts/2019-02-25-the-1902-release-of-remote-display-analyzer-rda/the-1902-release-of-remote-display-analyzer-feature-image.png
---
As you may have noticed this week a new release of Remote Display Analyzer (RDA) has been released. This release contains some interesting new features that can help you analyze the remote display protocol of various vendors. This blog post will cover some of the new features.

## What is Remote Display Analyzer?
Remote Display Analyzer, also known as RDA is a community tool that helps you analyze remoting protocols. It is compatible with the most used protocols from Microsoft, VMware, and Citrix. It allows you to validate settings and various statistics of the protocols. This is very useful when troubleshooting or in a use-case like remote branches with high latency. The tool allows you to switch settings on the fly to find the most optimal configuration.

The tool can be used for free using the community edition, but for the full feature set, the subscribed edition is required. Both can be found <a href="https://rdanalyzer.com/editions-2/" target="_blank" rel="noopener noreferrer">here</a>.

## What's new in this release
The official release notes of RDA are available right <a href="https://www.rdanalyzer.com/support/release-notes/" target="_blank" rel="noopener noreferrer">here.</a>

![rda-1902]({{site.baseurl}}/assets/images/posts/2019-02-25-the-1902-release-of-remote-display-analyzer-rda/rda-1902.png)

A lot of big software vendors are changing the version number to a year and month number. As this is becoming a new standard the RDA team decided to adopt this version number as the version number is now 1902.0.0530. This is a very small change that doesn't add value but this way it is very clear when this release is created which is valuable for support.

When troubleshooting or analyzing the protocols it is important to get the right context. The edition and release of Windows is important context next to the Citrix or VMware agent version. This release shows both Windows and the Citrix or VMware agent version so there is no confusion which version is running.

With high-resolution screens like 4K, the correct DPI configuration ensures the text displays is readable. RDA now shows the configured DPI scaling configuration.

More organizations have a GPU enabled VDI environment using NVIDIA technology. As explained in an <a href="https://www.logitblog.com/conflicted-results-with-nvidia-tesla-m10/" target="_blank" rel="noopener noreferrer">older post</a> it is important to fully utilize the resources of the GPU. The new release of RDA shows more resource usage information including the type of license and server address configured. This is very valuable to ensure the GPU is correctly utilized and if the license is working properly.

More details about this release can be found right <a href="https://www.rdanalyzer.com/support/release-notes/" target="_blank" rel="noopener noreferrer">here.</a>

## Conclusion
Remote Display Analyzer (RDA) is a great tool that should be in every IT Pro's toolset. It is very valuable to analyze and troubleshoot remoting protocols of the big vendors. RDA has a free community edition but to get the complete feature set the subscribed edition is required. RDA can be downloaded right <a href="https://rdanalyzer.com/order/" target="_blank" rel="noopener noreferrer">here</a>. If you have a good idea that needs to be added to RDA, let us know! Send us an email or just leave a comment below!

Photo by <a href="https://unsplash.com/photos/M-xaOaCzy_M?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener noreferrer">Bhushan Sadani</a> on <a href="https://unsplash.com/search/photos/party?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener noreferrer">Unsplash</a>
