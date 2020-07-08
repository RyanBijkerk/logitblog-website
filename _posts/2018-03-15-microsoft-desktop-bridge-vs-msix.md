---
layout: post
title: Microsoft Desktop Bridge vs MSIX
authors: [ryan]
comments: true
categories: [App-V]
tags: [App-V, AppX, MSIX, Packaging, Packaging, UWP]
image: assets/images/posts/2018-03-15-microsoft-desktop-bridge-vs-msix/microsoft-desktop-bridge-vs-msix-feature-image.png
---
Last week during the Microsoft Developers day the new package model MSIX has been announced. This is going to be the new model for all the applications, but what are the benefits compared to the current Desktop Bridge? In this blog post, I will share the difference and the benefit of MSIX.
<h2><!--more-->What is Desktop Bridge?</h2>
In Windows 10 1607 Microsoft has released Desktop Bridge which is also known as Project Centennial. This solution helps you to move your existing application to the Microsoft Store so you can leverage the full installation and update experience. It is also known as APPX packages. This helps with distributing your application globally to the Windows platform. It allows software vendors and developers to gain a better adoption in Windows 10. But it is important to know it is not just converting your applications. If you want to take full advantage of the platform additional work is required.
<h2>What is MSIX?</h2>
MSIX is going to be the new format which can replace both the traditional MSI as well as APPX which is the current Windows Store format. It will inherit all the features of UWP and it also introduces more container security options and allows application customizations. Microsoft open sourced the entire project on <a href="https://github.com/Microsoft/msix-packaging" target="_blank" rel="noopener">GitHub</a> as this will benefit the adoption in the market.
<h2>Why is MSIX better than Desktop Bridge?</h2>
<span style="display: inline !important; float: none; background-color: transparent; color: #777777; cursor: text; font-family: 'Open Sans',Helvetica,Arial,sans-serif; font-size: 14.86px; font-style: normal; font-variant: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: left; text-decoration: none; text-indent: 0px; text-transform: none; -webkit-text-stroke-width: 0px; white-space: normal; word-spacing: 0px;">Desktop Bridge was already the attempt to close the gap between Win32 applications and the Windows Store, MSIX is going to make this gap closer. As the Desktop Bridge primarily focused on the software vendors, enterprises could not always benefit the store as they often require customization for the applications. With MSIX Microsoft will allow customization so enterprises can tweak and tune the application to create the best user experience.</span>

<span style="display: inline !important; float: none; background-color: transparent; color: #777777; cursor: text; font-family: 'Open Sans',Helvetica,Arial,sans-serif; font-size: 14.86px; font-style: normal; font-variant: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: left; text-decoration: none; text-indent: 0px; text-transform: none; -webkit-text-stroke-width: 0px; white-space: normal; word-spacing: 0px;">These customizations will be separated in a different layer which increases agility with updates. As MSIX is built on the container technology like UWP it will provide more security options to be compatible with more applications.</span>
<h2>What about App-V?</h2>
App-V is a proven technology in enterprises and I guess MSIX won't replace App-V very soon. As Bram Wolfs already <a href="https://bramwolfs.com/2018/03/13/msix-the-platform-for-all-windows-applications/" target="_blank" rel="noopener">mentioned:</a>
<blockquote>If you currently using App-V to deploy and manage traditional application you are already ahead of the game.</blockquote>
Both solutions are container based technology. If Microsoft will provide a conversation to move your existing App-V package to MSIX it may change the position of App-V. Should you wait for MSIX? Nah, a lot of organizations are still investing in App-V even when the last two years there are no real innovations. App-V is integrated into Windows 10 so it will be there for a long time.
<h2>Conclusion</h2>
It will take some time to fully adopt MSIX in the market and for enterprise organizations, it is not interesting right now. It looks like a promising platform but just like Desktop Bridge, time will tell. Is it another failure, or will it be the successor of traditional packaging?

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/mTkXSSScrzw?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Leone Venter</a> on <a href="https://unsplash.com/search/photos/box?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>