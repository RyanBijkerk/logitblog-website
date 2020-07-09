---
layout: page
title: ConfigMgr Status Indicator
authors: [ryan]
comments: true
categories: [ConfigMgr Status Indicator]
---
<strong><img class="alignleft size-full wp-image-741" src="{{site.baseurl}}/assets/images/pages/configmgr-status-indicator/LoginConsultants.png" alt="" width="199" height="61" /></strong>Many organizations are using Microsoft System Center Configuration Manager (ConfigMgr) to manage the machines in their organization. Microsoft System Center Configuration Manager allows administrators to distribute and manage application software and updates to both users and machines, both physical and virtual.

The Microsoft Configuration Manager Client, which needs to be located on every machine, periodically checks with central Management Points if any software (or updates) is assigned to the local machine or the logged on user. When software is applicable it will determine whether it needs to be installed mandatory or be “presented” to the user who is logged on. Mandatory software (like corporate applications or software updates) will usually be installed on the background without any intervention from the end user.
<h2>The challenge</h2>
When Microsoft System Center Configuration Manager is installing software in the background it prevents the Microsoft Configuration Manager Client from installing other software. For mandatory (background) installation this is handled through a queue. Application installations that the end user initiates are not queued and the user is presented with a (not so user friendly) error:

<img class="size-full wp-image-729 alignnone" src="{{site.baseurl}}/assets/images/pages/configmgr-status-indicator/sccm-error.png" alt="" width="314" height="168" />

During one of our recent migrations this error was the one issue users complained about. In this particular migration laptops were deployed in advance, put on stock and swapped on the day of migration. Because of this approach users were sometimes presented with laptop configurations that were one or two months old. When these machines were reconnected to the network the Microsoft Configuration Manager Client immediately started updating the machine to the latest software and patch level.
However the users that were freshly migrated also needed to install their personal software and were presented with the error above when the system underneath was still updating.
Although the users accepted the fact that their system was updating they all had a single question: “when should we retry?”. Unfortunately there was no single answer to that question. Until now…
<h2>The solution</h2>
For this situation the ConfigMgr Status Indicator (CSI) was developed. CSI provides users with information about the activities of the underlying Microsoft Configuration Manager Client through a taskbar icon or through user installed Windows Gadgets.

<img class="alignnone size-full wp-image-1071" title="" src="{{site.baseurl}}/assets/images/pages/configmgr-status-indicator/CSI-status.png" alt="" width="200" height="191" />

CSI covers information about application installation or uninstallation but also informs the users about required reboots or logoff. Most importantly it informs the user when the Microsoft Configuration Manager Client is free of activities and user initiated installs can take place.
<h2>Where can I get more information?</h2>
More information can be found in the <a title="CSI Admin Guide" href="http://www.logitblog.com/downloads/CSI%20Admin%20Guide%20v1.0.pdf" target="_blank">CSI Admin Guide</a>.
<h2>How can I download CSI?</h2>
You can download CSI from the <a title="Login Consultants Downloads" href="http://www.loginconsultants.com/index.php?option=com_docman&amp;task=cat_view&amp;gid=20&amp;Itemid=149" target="_blank">Login Consultants</a> website in the download section. (registration required)
<h2>Feedback</h2>
If you have any suggestions or comments about CSI please let us know. This way we can improve CSI. We are also keep you up to date with some development updates at Logitblog.