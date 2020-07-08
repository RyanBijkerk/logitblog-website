---
layout: post
title: Delayed application start times with VMware UEM and App-V
authors: [niels]
comments: true
categories: [App-V]
tags: [App-V, UEM, VDI, VMware, VMware, XenDesktop]
image: assets/images/posts/2016-03-31-delayed-application-start-times-with-vmware-uem-and-app-v/delayed-application-start-times-with-vmware-uem-and-app-v-feature-image.png
---
User experience in centralized desktop environments is very important. If an application starts after 60 seconds this will have a big impact on the end user experience. This was identified when using VMware UEM in combination with Microsoft App-V. This blog post will explain the exact behaviour, the cause of the issue and a way to work around the problem.
<h2>The investigation</h2>
The behaviour was reported by users hosted in a VDI environment with a seat count of 10.000 delivered by Citrix XenDesktop. Application delivery is a combination of locally installed applications and Microsoft App-V. Profile management is done with Immidio Flex+ (now called VMware UEM).

To troubleshoot a reproduction of this behaviour is good starting point. Because it is  application related it make sense to involve the packaging team. It turns out the behavior could not be reproduced by the packaging team. A difference was found between the packaging machine and the production environment. The packaging team is using an isolated machine which is not domain joined without an anti-virus solution because this interferes with the sequencing process. The behaviour could be related to anti-virus solution or group policies.

The anti-virus solution was easily verified by removing this from the specific production machine. This did not remove the behaviour. So the next step is to check the configured group policies. A way to check this is to create an OU with block inheritance enabled so no policies were applied. This removed the behaviour.

In order to isolate the policies a one by one deduction was done until the behaviour was linked to VMware UEM and the DirectFlex feature.
<h2>Behavior of App-V with VMware UEM</h2>
When a user has to wait 60 seconds until he can interact with the application is in general a very bad experience. The behaviour is caused by the interaction between virtualized applications delivered by Microsoft App-V and locally installed applications combined with VMware UEM DirectFlex. When an App-V application starts a local child process this process is delayed for 60 seconds. The FlexEngine cannot interact with the general services because it is running inside the App-V bubble. The 60 seconds is a timeout of the DirectFlex mechanism. To get an idea how a user is experiencing this behavior we created an animation of a simulated scenario (click on the image to play).

<a href="{{site.baseurl}}/assets/images/posts/2016-03-31-delayed-application-start-times-with-vmware-uem-and-app-v/VMware-DirectFlex-Delayed.gif" rel="attachment wp-att-3494"><img class="alignnone size-large wp-image-3494" src="{{site.baseurl}}/assets/images/posts/2016-03-31-delayed-application-start-times-with-vmware-uem-and-app-v/VMware-DirectFlex-Delayed.gif" alt="VMware-DirectFlex-Delayed" width="584" height="329" /></a>

<h2>Solution from within VMware UEM</h2>
Unfortunately, there is no real solution because it has to do with the behavior of Microsoft App-V and the VMware UEM processes which cannot reach the required component within the bubble. In order to remove the behavior, you could consider disabling DirectFlex functionality from the applications that are started as a child process by an App-V application. This way DirectFlex is not started and waiting for the timeout.

<h2>Conclusion</h2>
This is not common behavior and probably a very specific scenario. It has to do with limitations within App-V in combination with VMware UEM. I hope this blog post gives you a broad idea on how to troubleshoot issues like these and how to create a workaround. It is recommended to report to VMware when encountering this behaviour so they are aware of the situation. If you have further questions or remarks please feel free to leave a comment.

Want to know more about <a href="http://twitter.com/nielsgeursen">Niels</a>, check out his own <a href="http://www.geursen.net">blog</a>!
