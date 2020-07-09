---
layout: page
title: GAP-IT
authors: [ryan]
comments: true
categories: [GAP-IT]
---
<img class="alignleft wp-image-2947 size-thumbnail" src="{{site.baseurl}}/assets/images/pages/gap-it/1411267832_clock.png" alt="" width="150" height="150" />

GAP-IT is a free tool which has the ability to provide an administrator extra insight in the App-V 5.0 publishing times. These times are reported per individual package which is useful information for further package optimizations.

It comes with functionality to scan through the Windows Event Log searching for the publishing and package events. Based on these events the publishing times can be calculated. In order to resolve the package name and version the App-V management server API is used. This functionality is optional and the a package name can be manually assigned.
<h2>Where can I download it?</h2>

| Type | Version | MD5 Hash |
| :--: | :-----: | :------: |
| Stable | [1.0.17125.2](http://www.logitblog.com/downloads/GAP-IT/master/1.0.17125.2/GAP-IT.zip){:target="_blank} | d6cdeb822d7c9ad3148886cc135022c2 |
| Develop | [0.0.17283.2](http://www.logitblog.com/downloads/GAP-IT/develop/0.0.17283.2/GAP-IT.zip){:target="_blank"} | 546e7625b2e0a31dcfb40ca207f9dd6a |

<h2>Requirements:</h2>
<ul>
	<li>Microsoft App-V 5.0 full streaming environment</li>
	<li>App-V Client 5.0</li>
	<li>.NET 4.5</li>
</ul>
<h2>How do I use GAP-IT?</h2>
This section will describe how to use the tool.
<table>
<tbody>
<tr>
<td width="198"><strong>Description</strong></td>
<td width="403"><strong>Screenshot</strong></td>
</tr>
<tr>
<td width="198">Extract the executable from the zip and run GAP-IT.exe</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/run-gap-it.png"><img class="alignleft size-medium wp-image-3184" src="{{site.baseurl}}/assets/images/pages/gap-it/run-gap-it.png" alt="run-gap-it" width="204" height="300" /></a></td>
</tr>
<tr>
<td width="198">Select the date for collect all the publishing events, please be aware selecting a longer time period will increase loading time</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/select-date-gap-it.png"><img class="alignleft size-medium wp-image-3185" src="{{site.baseurl}}/assets/images/pages/gap-it/select-date-gap-it.png" alt="select-date-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">Resolve the package name</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/enebale-resolve-gap-it.png"><img class="alignleft size-medium wp-image-3181" src="{{site.baseurl}}/assets/images/pages/gap-it/enebale-resolve-gap-it.png" alt="enebale-resolve-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">Select the protocol</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/select-protocol-gap-it.png"><img class="alignleft size-medium wp-image-3186" src="{{site.baseurl}}/assets/images/pages/gap-it/select-protocol-gap-it.png" alt="select-protocol-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">Provide the address of the App-V management server</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/provide-address-gap-it.png"><img class="alignleft size-medium wp-image-3183" src="{{site.baseurl}}/assets/images/pages/gap-it/provide-address-gap-it.png" alt="provide-address-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">Configure the port of the App-V management server</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/configure-port-gap-it.png"><img class="alignleft size-medium wp-image-3187" src="{{site.baseurl}}/assets/images/pages/gap-it/configure-port-gap-it.png" alt="configure-port-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">Click the "Collect data" button to collect the app-v publishing data</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/loading-gap-it.png"><img class="alignleft size-medium wp-image-3182" src="{{site.baseurl}}/assets/images/pages/gap-it/loading-gap-it.png" alt="loading-gap-it" width="300" height="230" /></a></td>
</tr>
<tr>
<td width="198">After the results are collect the charts will be displayed</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/data-gap-it.png"><img class="alignleft size-medium wp-image-3188" src="{{site.baseurl}}/assets/images/pages/gap-it/data-gap-it.png" alt="data-gap-it" width="300" height="230" /></a></td>
</tr>
</tbody>
</table>
<h2>Settings</h2>
This section will describe where the settings are stored.
<table>
<tbody>
<tr>
<td width="198"><strong>Description</strong></td>
<td width="403"><strong>Screenshot</strong></td>
</tr>
<tr>
<td width="198">The setting are stored within the registry and can be found in:“HKCU\Software\Logitblog\GAP-IT”</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/registry-gap-it.png"><img class="alignleft size-medium wp-image-3190" src="{{site.baseurl}}/assets/images/pages/gap-it/registry-gap-it.png" alt="registry-gap-it" width="300" height="286" /></a></td>
</tr>
<tr>
<td width="198">All collected packages are saved within the registry and the package name can be renamed to user friendly name</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/registry-package-gap-it.png"><img class="alignleft size-medium wp-image-3192" src="{{site.baseurl}}/assets/images/pages/gap-it/registry-package-gap-it.png" alt="registry-package-gap-it" width="300" height="286" /></a></td>
</tr>
<tr>
<td width="198">Edit the registry entry and adjust the package name[package name]/[version]</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/registry-package-edit-gap-it.png"><img class="alignleft size-medium wp-image-3191" src="{{site.baseurl}}/assets/images/pages/gap-it/registry-package-edit-gap-it.png" alt="registry-package-edit-gap-it" width="300" height="133" /></a></td>
</tr>
<tr>
<td width="198">Collect the results the new package name is applied</td>
<td width="403"> <a href="{{site.baseurl}}/assets/images/pages/gap-it/packagename-change-gap-it.png"><img class="alignleft size-medium wp-image-3189" src="{{site.baseurl}}/assets/images/pages/gap-it/packagename-change-gap-it.png" alt="packagename-change-gap-it" width="300" height="230" /></a></td>
</tr>
</tbody>
</table>
<h2>Feedback or ideas</h2>
If you have any feedback on the tool please feel free to get in contact with me. I'm always open to new ideas or ways to improve the tool. Provide feedback by sending me an email to ryan[at]logitblog.com or provide it in the comments below.
