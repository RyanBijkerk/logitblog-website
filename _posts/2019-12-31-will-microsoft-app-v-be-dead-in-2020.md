---
layout: post
hidden: false
title: Will Microsoft App-V be dead in 2020?
authors: [ryan]
categories: [App-V]
tags: [App-V, Microsoft, MSIX]
image: assets/images/posts/2019-12-31-will-microsoft-app-v-be-dead-in-2020/will-microsoft-appv-be-dead-in-2020-feature-image.png
---
Microsoft App-V has been around for a long time, and is used in many organizations. But ever since the native App-V integration in Windows 10, there is a lack of update information. Should you still invest in Microsoft App-V with the rise of MSIX? Is App-V really dead? In this blog post, I will share some facts and thoughts about App-V.

## Microsoft App-V integration in Windows 10
Microsoft has integrated the App-V client in Windows 10 since the 1607 release. That is over 3 years ago, and since then there been minimal communication regarding any updates. As the client is integrated with the operating system, all the updates and fixes are release using Windows Updates. This makes it very hard to keep track on the fixes and the potential issues. The Microsoft App-V documentation website does not have any information regarding updates or fixes. The latest "Whats new in App-V for Windows 10" is regarding Windows 10 1703, which is released over 2 years ago. Luckily, we can always count on the App-V guru [Tim Mangan](https://www.tmurgent.com/appv/en/){:target="_blank"}. On Tim's [website](https://www.tmurgent.com/AppV/en/resources/app-v-5-versions/509-5-2-appvmonthlies){:target="_blank"} you can find KB patches that address issues in the App-V client. Please note, this is not official Microsoft documentation and created on best effort by Tim.

The following table shows some recent updates of Microsoft App-V client.

<table class="table ng-scope">
<tbody>
<tr>
<td width="137"><strong>Issue</strong></td>
<td width="48"><strong>1909</strong></td>
<td width="81"><strong>1903</strong></td>
<td><strong>1809</strong></td>
<td><strong>1803</strong></td>
<td><strong>1709</strong></td>
<td><strong>Notes</strong></td>
</tr>
<tr>
<td>‘Addresses an issue that prevents a Microsoft&nbsp;App-V application from opening and displays a network failure error. This issue occurs under certain circumstances, such as when a system’s battery is low or there is an unexpected power failure.’</td>
<td width="48">none</td>
<td width="81">Sept 26, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4517211">KB4517211</a></p>
<p>(OS Build 18362.387)</p></td>
<td>Sept 24, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4516077">KB4516077</a></p>
<p>(OS Build 17763.774)</p></td>
<td>Sept 24, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4516045">KB4516045</a></p>
<p>(OS Build 17134.1039)</p></td>
<td>Sept 24, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4516071">KB4516071</a></p>
<p>(OS Build 16299.1420)</p></td>
<td>The described issue is listed as resolved in both the Sept patches AND the July patches for 1903 and August for 1809/1803.&nbsp; Use the September one to be sure.</td>
</tr>
<tr>
<td>‘Addresses an issue that prevents Microsoft App-V from handling a parameter of the&nbsp;CreateProcess&nbsp;API properly, which prevents the virtual process from opening.’</td>
<td width="48">none</td>
<td width="81">Sept 26, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4517211">KB4517211</a></p>
<p>(OS Build 18362.387)</p></td>
<td>none</td>
<td>none</td>
<td>none</td>
<td>&nbsp;</td>
</tr>
<tr>
<td>“Addresses an issue that prevents Microsoft Application Virtualization (App-V) scripting from working if you run it when you’re not connected to a domain controller (DC). App-V scripting also fails when you run it in an environment that only contains Microsoft Azure Active Directory. ”<p></p>
<p>“Addresses an issue that prevents an App-V application from opening if the client is offline and a startup script is defined for the App-V application.”</p></td>
<td width="48">none</td>
<td width="81">July 26, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4505903">KB4505903&nbsp;</a></p>
<p>(OS Build 18362.267)</p></td>
<td>July 22, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4505658">KB4505658</a></p>
<p>(OS Build 17763.652)</p></td>
<td>July 16, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4507466">KB4507466</a></p>
<p>(OS Build 17134.915)</p></td>
<td>July 16, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4507465">KB4507465</a></p>
<p>(OS Build 16299.1296)</p></td>
<td>These two items appear independently in the same KB for 1903, but only the first text appears in the 1809/1803/1709 KBs. They appear to be slightly different situations of the same problem, likely fixed with the same code change in all cases.<p></p>
<p>Wow!&nbsp;&nbsp;This has been a long time feature request (well, we documented it as a bug but were told that it was per-design).&nbsp; Presumably this was addressed for the Azure Active Directory scenarios that are not co-managed. But it should mean that App-V scripts now work on non-domain joined machines.</p></td>
</tr>
<tr>
<td>“Addresses an issue that causes Office 365 applications to stop working after opening when they are deployed as App-V packages. “</td>
<td width="48">none</td>
<td width="81">June 27, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4501375">KB4501375</a></p>
<p>(OS Build 18362.207)</p></td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4501371">KB4501371</a></p>
<p>(OS Build 17763.592)</p></td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4503288">KB4503288</a></p>
<p>(OS Build 17134.858)</p></td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4503281">KB4503281</a></p>
<p>(OS Build 16299.1237)</p></td>
<td>&nbsp;</td>
</tr>
<tr>
<td>“Addresses an issue that fails to update a user hive when you publish an optional package in a Connection Group after the Connection Group was previously published.”</td>
<td width="48">none</td>
<td width="81">none</td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4501371">KB4501371</a></p>
<p>(OS Build 17763.592)</p></td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4503288">KB4503288</a></p>
<p>(OS Build 17134.858)</p></td>
<td>June 18, 2019<p></p>
<p><a href="https://support.microsoft.com/en-us/help/4503281">KB4503281</a></p>
<p>(OS Build 16299.1237</p></td>
<td></td>
</tr>
</tbody>
</table>

Source: [https://www.tmurgent.com/AppV/en/resources/app-v-5-versions/509-5-2-appvmonthlies](https://www.tmurgent.com/AppV/en/resources/app-v-5-versions/509-5-2-appvmonthlies){:target="_blank"}

## Support lifecycle Microsoft App-V
It is important to understand the support lifecycle has changed with the integration of App-V in Windows 10. The support lifecycle of the native integrated App-V client is based on the Windows 10 build. As the support ends for the Windows 10 build, it also applies to the App-V client.

The lifecycle of the various Windows 10 builds are specified in the table below.
<table class="table ng-scope">
<thead>
<tr>
<th class="" scope="col">Windows 10 version history</th>
<th scope="col">Date of availability</th>
<th scope="col">End of service for Home, Pro, Pro Education, and Pro for Workstations editions</th>
<th scope="col">End of service for Enterprise and Education editions</th>
</tr>
</thead>
<tbody>
<tr>
<td>Windows 10, version 1909</td>
<td>November 12, 2019</td>
<td>May 11, 2021</td>
<td>May 10, 2022**</td>
</tr>
<tr>
<td>Windows 10, version 1903</td>
<td>May 21, 2019</td>
<td>December 8, 2020</td>
<td>December 8, 2020</td>
</tr>
<tr>
<td>Windows 10, version 1809</td>
<td>November 13, 2018</td>
<td>May 12, 2020</td>
<td>May 11, 2021**</td>
</tr>
<tr>
<td>Windows 10, version 1803</td>
<td>April 30, 2018</td>
<td>November 12, 2019</td>
<td>November 10, 2020</td>
</tr>
<tr>
<td>Windows 10, version 1709</td>
<td>October 17, 2017</td>
<td>April 9, 2019</td>
<td>April 14, 2020</td>
</tr>
<tr>
<td>Windows 10, version 1703</td>
<td>April 5, 2017*</td>
<td>October 9, 2018</td>
<td>October 8, 2019</td>
</tr>
<tr>
<td>Windows 10, version 1607</td>
<td class="">August 2, 2016</td>
<td class="">April 10, 2018</td>
<td class="">April 9, 2019</td>
</tr>
<tr>
<td>Windows 10, version 1511</td>
<td>November 10, 2015</td>
<td>October 10, 2017</td>
<td class="">October 10, 2017</td>
</tr>
<tr>
<td>Windows 10, released July 2015 (version 1507)</td>
<td>July 29, 2015</td>
<td>May 9, 2017</td>
<td>May 9, 2017</td>
</tr>
</tbody>
</table>

Source: [support.microsoft.com](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet){:target="_blank"}

When using an older operating system the following support dates apply to App-V version 5.1.

<table class="table" role="table" aria-label="Product search results">
<thead>
<tr>
<th class="name-cloumn"><span id="sort-by-name" class="sortable ng-binding ascending active" tabindex="0" role="button">Products Released</span></th>
<th class="date-column"><span id="sort-by-startdate" class="sortable ng-binding ascending" tabindex="0" role="button">Lifecycle Start Date</span></th>
<th class="date-column"><span id="sort-by-msedate" class="sortable ng-binding ascending" tabindex="0" role="button">Mainstream Support End Date</span></th>
<th class="date-column"><span id="sort-by-esedate" class="sortable ng-binding ascending" tabindex="0" role="button">Extended Support End Date</span></th>
</tr>
</thead>
<tbody>
<tr class="ng-scope">
<td class="name-cloumn text-body ng-binding">Microsoft Application Virtualization 5.1 for Remote Desktop Services</td>
<td class="date-column text-body ng-binding">11/1/2015</td>
<td class="date-column text-body ng-binding">1/9/2018</td>
<td class="date-column text-body ng-binding">4/14/2026</td>
</tr>
<tr class="ng-scope">
<td class="name-cloumn text-body ng-binding">Microsoft Application Virtualization 5.1 for Windows Desktops</td>
<td class="date-column text-body ng-binding">11/1/2015</td>
<td class="date-column text-body ng-binding">1/9/2018</td>
<td class="date-column text-body ng-binding">4/14/2026</td>
</tr>
<tr class="ng-scope">
<td class="name-cloumn text-body ng-binding">Microsoft Application Virtualization Hosting 5.1 for Windows Desktops</td>
<td class="date-column text-body ng-binding">11/1/2015</td>
<td class="date-column text-body ng-binding">1/9/2018</td>
<td class="date-column text-body ng-binding">4/14/2026</td>
</tr>
</tbody>
</table>

Source: [support.microsoft.com](https://support.microsoft.com/en-us/lifecycle/search?alpha=Microsoft%20Application%20Virtualization%205.1){:target="_blank"}

## The rise of MSIX and MSIX App Attach
In 2018 Microsoft has introduced a new packaging format called MSIX. I have even written a hands-on blog post about it, right [here]({{site.baseurl}}/hands-on-msix/){:target="_blank"}. MSIX is going to be the new format which can replace both the traditional MSI and setups as well as AppX which is the current Microsoft Store format. MSIX will inherit all the features of UWP and it also introduces more container security options and allows application customization's. Existing App-V packages can be converted to the new MSIX format. This allows organizations to convert App-V packages to the MSIX format and make them available from the Microsoft Store for Business.

This year Microsoft also introduced MSIX App Attach, allowing administrators to separate the applications from operating system in a dedicated layer. This layering technique provides better management while keeping the native application user experience. Using MSIX App Attach still provides the isolation using MSIX technology but also provides the ability to bundle multiple MSIX applications. The following video contains an excellent explanation.

<iframe width="100%" height="500" src="https://www.youtube.com/embed/QLDu6QVohEI?start=66" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Microsoft App-V is dead
Yes, besides Microsoft App-V is still supported and maintained, it is dead. It is clear since the integration of the App-V client in Windows 10, Microsoft has put minimal effort in this technology. There is minimal, to no information available on the various changes and fixes in the App-V client. Nevertheless, Microsoft App-V will be around for many years. Many organizations invested a lot of time and resources to convert applications to App-V packages. The transition to a new packaging format, like MSIX, is not done over night. But looking at the investment in MSIX and App Attach using the traction of Windows Virtual Desktop, the new strategy is clearly not App-V. Therefore Microsoft App-V is dead.

I think Microsoft is going to continue to invest in MSIX in 2020, making Microsoft App-V obsolete. Therefore, I would not recommend to organizations to invest in Microsoft App-V anymore. In 2020 it will be the time to prepare for adoption and transition to MSIX as the new standard for application virtualization, isolation and deployment. What do you think will happen with Microsoft App-V? Leave your thoughts in the comments below.

Photo by [Eliott Van Buggenhout](https://unsplash.com/@eli29?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/s/photos/shipwreck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}