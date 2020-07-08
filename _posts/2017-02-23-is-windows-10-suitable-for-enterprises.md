---
layout: post
title: Is Windows 10 suitable for enterprises?
authors: [ryan]
comments: true
categories: [Microsoft]
tags: [Agile, CB, CBB, LTSB, Microsoft, Windows 10, Windows 10]
image: assets/images/posts/2017-02-23-is-windows-10-suitable-for-enterprises/is-windows-10-suitable-for-enterprises-feature-images.png
---
With the introduction of Windows 10 Microsoft has decided to change the servicing strategy. This new strategy has a more continuous delivery approach with bigger feature updates every 6 months. Many enterprise organizations are investigating the move to Windows 10. The servicing strategy proves to be a difficult change. The main question is whether Windows 10 is suitable for enterprises? This blog post explains the basics of the different Windows 10 branches and points at the challenges introduced by Windows 10 in an enterprise organization.

<h2>Servicing strategies of Windows 10</h2>
In order to understand the challenge of introducing Windows 10 in enterprises we first have to start with understanding the servicing strategy. As this is well described by Microsoft, I will provide a short summary only. There are four different branches of Windows 10:
<ul>
 	<li>Insider Preview,</li>
 	<li>Current Branch,</li>
 	<li>Current Branch for Business,</li>
 	<li>Long-Term Serving Branch.</li>
</ul>
<strong>Insider Preview </strong>is the latest development build of Windows that will contain the newest features. As this build is still in development some features may not work as expected. This preview is meant for those who want to be involved with the latest changes and provide feedback to Microsoft in order to improve Windows.

<strong>Current Branch (CB)</strong> is the default branch when installing Windows 10. This contains the official releases of the latest features as soon Microsoft releases them. In general, the Current Branch is used for pilot deployments and testing of Windows feature updates or for users, such as developers, who prefer to work with the latest features.

<strong>Current Branch for Business (CBB)</strong> is meant for organizations that prefer to have a testing cycle before deploying new features. The Current Branch for Business is about 4 months behind on the Current Branch. Microsoft describes that both CB and CBB have their purpose in the overall deployment process for an enterprise.

<strong>Long-Term Servicing Branch</strong> <strong>(LTSB) </strong>is aimed at specialized systems such as machines that control medical equipment, point-of-sale systems or AMTs, etc. These systems typically perform a single task and don't need feature updates as frequently as other devices in the organization. Features like Microsoft Edge, Windows Store client, Cortana, Mail, Calander, OneNote, News, Weather, Sports, Money, Photos, Camera, Music, and Clock are not included. Microsoft never publishes feature updates through Windows Update on devices that run Windows 10 Enterprise LTSB. Instead, it typically offers new LTSB releases every 2–3 years and organizations can choose to install them as in-place upgrades or even skip releases over a 10-year life cycle.

More information about the different branched can be found <a href="https://technet.microsoft.com/en-us/itpro/windows/manage/waas-overview">here</a>.

The following illustration helps to get a better understanding of the different release cycles.

<a href="{{site.baseurl}}/assets/images/posts/2017-02-23-is-windows-10-suitable-for-enterprises/windows-10-branches.png"><img class="alignnone wp-image-4216 size-large" src="{{site.baseurl}}/assets/images/posts/2017-02-23-is-windows-10-suitable-for-enterprises/windows-10-branches.png" width="790" height="444" /></a>

The release frequency will increase with two updates scheduled for this year as shown in the illustration below.

<a href="{{site.baseurl}}/assets/images/posts/2017-02-23-is-windows-10-suitable-for-enterprises/windows-10-release-cadence.png"><img class="alignnone wp-image-4217 size-full" src="{{site.baseurl}}/assets/images/posts/2017-02-23-is-windows-10-suitable-for-enterprises/windows-10-release-cadence.png" width="797" height="437" /></a>
<h2>Working applications</h2>
"It is all about the apps", is an often heard phrase in the community. In order to run a business, applications are required these days no matter the size of the organization. When moving to a new operating system you have to make sure the applications remain working as expected. Usually, enterprise organizations have a high quantity of applications and when moving to Windows 10 they have to validate each of them individually. With the new servicing strategy, this validation cycle repeats for each feature release and needs to be completed within 4 months. Let's say you have 3000 applications, is it realistic to test them all in 4 months?

There are solutions like Citrix AppDNA that can help determining whether an application is compatible with Windows 10, but how reliable is the data they provide? In my opinion, the only way to really find out the answer is to actually install and test the application on Windows 10. Or are there alternatives?
<h2>LTSB or CCB?</h2>
Should you use LTSB or CCB when you migrate to Windows 10? If you follow the Microsoft recommendation, you should use CCB and not LTSB because the latter is meant for specialized systems. But looking at the servicing strategy LTSB allows enterprises to compose stable corporate images. Over time I have created a personal preference for LSTB. Why? Because there is a big difference in default apps when comparing both installations and I like the cleaner LTSB. I don't mind having the extra apps on my personal device but on my corporate device, I want apps like Visual Studio and Office, not games like Candy Crush Soda Saga (installed by default). Also from a performance perspective, LTSB is cleaner compared to CB and CCB because its number of running processes is less by default. For more information about the performance difference, keep an eye out at the <a href="http://www.loginvsi.com/blog" target="_blank">Login VSI blog</a>.

Still, I wonder, though I prefer LTSB because it's cleaner, is it also viable for enterprises? After all, Microsoft does not recommend this. What do you think?
<h2>Agile mindset</h2>
Microsoft is moving to an Agile development strategy with frequent releases of their software. This allows Microsoft to quickly adapt to customer needs. But are other non-software development organizations ready for this Agile approach? Terms like continuous delivery and integration testing are very common in software development but not yet in enterprise IT. As DevOps is becoming more and more popular, how many organizations are truly applying DevOps the right way? On thing for sure, in order to successfully move to Windows 10 automation is key. By having automated deployment, automated testing and automated validation of your application landscape you can focus more on the problem areas to keep at speed with the release cycles.
<h2>Conclusion</h2>
Many enterprise organizations are looking into migrating to Windows 10 which comes with a big challenge. Because of the new servicing strategy, Microsoft increases agility in releasing new functionalities but this requires the same Agile mindset in enterprise organizations. By using LTSB, organizations can maintain their traditional deployment cycles but could miss opportunities the latest features offer. In order to use the recommended CBB branch automation is key. Only this way you can successfully test the application's compatibility in enterprise organizations to keep up with the Windows 10 release cycle. Do you see the same challenge within your organization? Please share your experience or thoughts in the comments below.
