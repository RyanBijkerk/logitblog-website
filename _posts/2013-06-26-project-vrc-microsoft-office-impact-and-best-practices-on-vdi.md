---
layout: post
title: 'Project VRC: Microsoft Office Impact and Best Practices on VDI'
authors: [ryan]
comments: true
categories: [Microsoft Office]
tags: [Impact, Login VSI, Microsoft Office, Office 2007, Office 2010, Office 2013, Project VRC, Project VRC, VDI]
image: assets/images/posts/2013-06-26-project-vrc-microsoft-office-impact-and-best-practices-on-vdi/project-vrc-microsoft-office-impact-and-best-practices-on -vdi-feature-image.png
---
Microsoft Office is by far the most used application suite in the corporate environment. The Office suite is almost always deployed and used in virtual desktop infrastructures. But what is the performance impact of upgrading Microsoft Office?

This whitepaper focuses on the performance and capacity impact of the three most recent Office versions in VDI running Windows 7. Microsoft Office 2007, Office 2010 and Office 2013 are used with both x86 and x64 versions of Windows and Microsoft Office.

Comparing both Microsoft Office 2010 and 2013 with Office 2007 there is a negligible capacity impact of 1% with Office 2010, but there is a very substantial difference of 20% with Office 2013. As a result, upgrading to Office 2013 requires 20% more VDI capacity in comparison to Office 2007 and Office 2010.

Office 2013 will consistently use more CPU and much more memory. For instance, Office 2010 will consume 26% more memory than Office 2007. However, Office 2013 uses a rather momentous 272% (averaged) more memory on an application level.

Project VRC also tested possible performance optimizations for 2013 such as “animations off” and “hardware graphics acceleration off”. These optimization did not make a positive performance difference.

Reviewing Office 2010 x86 and x64 running on Windows 7 x64 it is possible to conclude that the x64 bit architecture had no significant impact on VSImax, when CPU is the only limiting factor and enough memory and IO capacity is available. However, running x64 version of both Windows and Office will have substantial impact on storage IOPS and memory footprint. For instance, Microsoft Office 2010 x64 running on Windows 7 x64 will consume 32% more memory compared to Office 2010 x86 running on Windows 7 x86.

Also indexing (search) was reviewed and how it affects VDI capacity. Disabling indexing is considered a performance best-practice, however, it is in highly appreciated feature by Office Users. If enough IO capacity is available, enabling indexing has only a 3% impact. If storage allows a potential 30% increase in write IO’s, it difficult to recommend disabling search, since it is such a critical feature to user acceptance.

Many organization consider an upgrade to Office 2013 for its functional benefits. However, upgrading to Microsoft Office 2013 will have a significant user capacity impact within VDI. The impact of Office 2013 on CPU, memory and disk IO is considerably higher than Office 2007 and 2010. Currently, there are no obvious tuning best practices available to lower Office 2013’s performance impact. It is therefore highly recommended to evaluate the performance impact of Office 2013 in your own environment, before it is deployed.

The full whitepaper can be downloaded <a href="http://www.projectvrc.com/login?return=L3doaXRlLXBhcGVycy9kb2NfZG93bmxvYWQvMTQtcGhhc2UtNi1taWNyb3NvZnQtb2ZmaWNlLWltcGFjdC1hbmQtYmVzdC1wcmFjdGljZXMtb24tdmRpLXYxLTA=" target="_blank">here</a> (registration required).
