---
layout: post
hidden: false
title: Sending messages to Slack using PowerShell
authors: [ryan]
categories: [PowerShell]
tags: [GO-EUC, Slack, Webhook, PowerShell]
image: assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/sending-messages-to-slack-using-powershell-feature-image.png
---
Slack is very popular within a lot of organizations and communities. It is a direct communication platform which is very powerful which even can replace emails. Now Slack can also be used to send automated messages. This blog post will explain how to send messages to Slack using PowerShell.

## What is Slack?
For those who don’t know what Slack, Slack is a collaboration hub where you and your team can work together to get things done. From project kickoffs to budget discussions, and to everything in between — Slack has you covered.

## Key features
💬 Organized conversations
  * In Slack, work happens in channels. You can [create channels](https://get.slack.help/hc/en-us/articles/201402297-Create-a-channel){:target="_blank"} based on teams, projects, or even office locations. Members of a workspace or org can join and leave channels as needed.

🔎 Searchable history
  * When messages and files are shared in public channels, information flows transparently throughout Slack. Search is how you can narrow down and find what you need. Learn how to [search for messages and files](https://get.slack.help/hc/en-us/articles/202528808){:target="_blank"}.

👥 Work with external partners
  * [Shared channels](https://get.slack.help/hc/en-us/articles/115004151203){:target="_blank"} and [guest accounts](https://get.slack.help/hc/en-us/articles/202518103){:target="_blank"} give you the flexibility and freedom to work with clients, contractors and other third parties without having to leave Slack.

🤖 Apps and integrations
  * Apps and integrations are the tools that will help you to bring your existing workflows into Slack. The [Slack App Directory](https://get.slack.help/hc/en-us/articles/360001537467-Learn-about-apps-and-the-App-Directory){:target="_blank"} has thousands of apps that you can integrate into Slack.

🔐 Security
  * At Slack, security is important to us: we protect your data as if it were our own. Learn more about our [security features](https://slack.com/security){:target="_blank"}.

For more information please visit the Slack website.

Source: [https://get.slack.help/hc/en-us/articles/115004071768-What-is-Slack](https://get.slack.help/hc/en-us/articles/115004071768-What-is-Slack-){:target="_blank"}.

## Sending a notification with PowerShell
Before sending any updates to a Slack channel you need to create an “Incoming Webhook”. Within the Admin Center open the menu and select “Configure Apps”.

![slack-admin-setting]({{site.baseurl}}/assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/slack-admin-settings.png)

Select the “Customer Integrations” option.

![slack-admin-config-app]({{site.baseurl}}/assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/slack-admin-configure-app.png)

Here you can add a new configuration.

![slack-admin-webhook]({{site.baseurl}}/assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/slack-admin-webhook.png)

Within the configuration of the Webhook, you have the possibility to select a channel and provide a name, description and an icon. The important part that you need to have is the “Webhook URL” as this your custom URL for posting the messages.

> Important: there is no authentication required so keep you Webhook URL private!

![slack-admin-webhook-setting]({{site.baseurl}}/assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/slack-admin-webhook-settings.png)

Now that the Webhook URL is created you need to create a JSON object that will contain the message that will be sent to the Slack Channel. In this example, it will send a simple Hello World message.

```lang-powershell
$uriSlack = "https://hooks.slack.com/services/{guids}"
$body = ConvertTo-Json @{
    pretext = "Hello world"
    text = "This is the text below with a blue thingy next to it"
    color = "#142954"
}

try {
    Invoke-RestMethod -uri $uriSlack -Method Post -body $body -ContentType 'application/json' | Out-Null
} catch {
    Write-Error (Get-Date) ": Update to Slack went wrong..."
}
```

Now sending this message to the Slack channel looks like this.

![slack-notification]({{site.baseurl}}/assets/images/posts/2019-07-30-sending-messages-to-slack-using-powershell/slack-notification.png)

This is just a simple example and there are a lot of different options and formats available. I really recommend reviewing some of the examples on the [Slack API website](https://api.slack.com/incoming-webhooks){:target="_blank"}.

## Conclusion
Slack is a perfect medium to quickly interact with each other. It also suits perfectly to send automated messages using a simple WebHook.

Within the [GO-EUC Slack channel](https://go-euc.slack.com/){:target="_blank"}, we use this to send the progress of our testing including the test results once all the tests are finished. This way we can keep track of the running tests and other members get a sneak preview of the latest results.

If you have any question or comments, please leave them below.

Photo by [Scott Webb](https://unsplash.com/@scottwebb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/search/photos/slack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}
