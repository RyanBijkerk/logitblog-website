---
layout: post
published: true
title: Audio troubles with Citrix Virtual Desktop 1811
authors: [ryan]
categories: [Citrix]
tags: [Audio, Bug, VDA, Windows 10 1803, Citrix]
image: assets/images/posts/2019-11-29-audio-troubles-with-citrix-virtual-desktop-1811/
---
Last couple weeks together with a colleague we have been investigating an issue regarding audio in a Citrix Virtual Desktop environment. For some reason the volume of the audio resets to 100% resulting in unexpected loud sounds. This blog post will explain the what we have found so far regarding this issue.

## Context of the audio issue
In order to understand the issue itself it is important to have the correct context. This is happening in a Citrix Virtual Desktop environment running version 1811 with Windows 10 1803 as the default operating system. There are multiple AudioEndpoint devices for which we noticed some weird behaviour. In most cases a ThinClient is used running Windows 10 LTSC which is connected to 2 monitors . These monitors have built-in speakers which are default set as primary audio device. Users with a personal device mostly work while connected through a port replicator like the Fujitsu Display Link or the HP Universal USB-C Dock. These port replicators are also connected to 2 monitors with built-in speakers. Users with a personal device have the ability to switch between default audio devices.

## When does the audio issue occur?
There are two primary situations in which the audio resets to a 100% volume level. When the user locks the connecting device when working on a laptop or ThinClient, both displays will go into standby after a couple minutes because of Power saving settings. When the user unlocks the device the volume is reset to a 100%. While the local client is Locked, the Citrix session remains active and running on the background, so there is no disconnect or reconnect tacking place. This also happens when taking over a session from another device. When I have a session open on my personal device and take it over from a ThinClient, the audio will be reset at a 100%.

## Citrix Audio Volume Persistent tool
Back in 2016 Citrix released a small tool called the Citrix Audio Volume Persistent tool.

> When a user reconnects to a XenApp session, all playback audio devices are reset to 100% volume level. Any playback volume settings previously made in the session are lost. This may also happen with a XenDesktop session. This tool restores the volume settings from values stored in the registry.
> 
> Source: [https://support.citrix.com/article/CTX206888](https://support.citrix.com/article/CTX206888){:target="_blank"}

Looking at the description this tool should have solved the described issue. But since we do not reconnect to the Citrix session the tool doesn’t work, since it will not be triggered, which it requires to fix the volume level. This means the tool will not solve this issue.

## What’s happening under the hood
As this was impacting a majority of the users we both investigated this issue and found some weird behaviour:

An AudioEndpoint device is listed as plug and play device which can be listed using the following PowerShell command line
```powershell
Get-PnpDevice -Class AudioEndpoint -Status Ok | Select Status, FriendlyName, InstanceId
```
