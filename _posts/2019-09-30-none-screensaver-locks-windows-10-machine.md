---
layout: post
hidden: false
title: None screensaver locks Windows 10 machine
authors: [ryan]
categories: [Microsoft]
tags: [Windows 10, Screensaver, Bug]
image: assets/images/posts/2019-09-30-none-screensaver-locks-windows-10-machine/blank-screensaver-locks-windows-10-machine-feature-image.png
---
Sometimes you encounter strange behavior which cannot be explained while troubleshooting. In this case while none screensaver was configured the machine still locks on the last configured screensaver wait time. This blog post will explain the strange screensaver behavior including the solution to resolve this issue.

## Screensaver behavior
There are multiple reasons why you would configure a screensaver. In this specific use-case it was a requirement for a VDI environment. Based on the connecting device specific screensaver policies are configured using a UEM solution. When connected from a not managed device a blank screensaver will be configured with a wait time of 15 minutes. When reconnecting from a managed device this will be switched to none screensaver without any timeout. While polices where correctly applied somehow the VDI desktop will still lock after 15 minutes. This resulted a double logon from the managed device and the VDI session, which has a very negative user experience.

Even when the screensaver is not configured using Group Policies this behavior still applies when it is configured manually. Somehow the settings from the “(none)” screensaver is used, which is based on the last configured wait time. For example, when configuring a screensaver with a wait time of two minutes, screensaver will work after two minutes. When switching to the “(none)” screensaver the wait time is grayed out with the value of two minutes. After the two minutes the device will be locked, even when it should not.

Further in this blog post there is a video that will show behavior including the solution. To be clear, this is not specific to a VDI environment and can be reproduced on any Windows 10 device.

## The solution
This behavior is caused by one specific group policy, “Interactive logon: Machine inactivity limit” which can be found in:

Computer Configuration -> Windows Settings -> Security Settings -> Local Policies -> Security Options.

![screensaver-gpo]({{site.baseurl}}/assets/images/posts/2019-09-30-none-screensaver-locks-windows-10-machine/local-group-policy-editor-machine-inactivity-limit.png)

The description of the policies is as followed:

> Windows notices inactivity of a logon session, and if the amount of inactive time exceeds the inactivity limit, then the screen saver will run, locking the session.
> 
> Default: not enforced.

As described this policy setting is not enforced by default, however, it is included in the Microsoft Security baseline. The Microsoft security baseline configures the policy with a value of 900 (15 minutes). More information about the Microsoft security baseline can be found [here](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-security-baselines){:target="_blank"}.

Although the value is 900 (15 minutes) the lock action will be done based on the last configured wait time of the screensaver. When the value is set to 0 or the policies is removed the behavior is solved. The video below shows the behavior reproduced on my own laptop which is not part of any domain. The video also included the solution using the Local Group Policy Editor.

<iframe width="100%" height="500" src="https://www.youtube.com/embed/_78oEFHexbs" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Conclusion
Sometimes you encounter these strange issues that can hunt you for days. In this case it took me way too much time to figure out the solution. This really looks like a bug in Windows 10 as the timeout configuration from the “Interactive logon: Machine inactivity limit” policy is not used. I’m not sure if this applies to all Windows 10 versions but I was able to reproduce it on build, 1803 and 1903. I have created a bug in the feedback hub which can be found [here](https://aka.ms/AA67rk3){:target="_blank"}. If you encounter the same issue ensure to upvote the bug so this would get attention at Microsoft.

I hope this blog post helps and if you ever encounter this exact behavior please let me know if this solution solves your issue by leaving a comment below.

Photo by [Aniket Deole](https://unsplash.com/@anik3t?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/s/photos/yosemite?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}