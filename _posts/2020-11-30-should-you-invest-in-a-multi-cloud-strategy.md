---
layout: post
hidden: false
title: Should you invest in a multi-cloud strategy?
authors: [ryan]
categories: [Cloud]
tags: [Azure, AWS, GPC, Architecture]
image: assets/images/posts/2020-11-30-should-you-invest-in-a-multi-cloud-strategy/should-you-invest-in-a-multi-cloud-strategy-feature-image.png
---
Should you invest in a multi-cloud strategy? Many organizations are transitioning to the cloud but why bet on a single horse? This question came up during a major outage at AWS that hit North America last week? This blog post will investigate some cloud strategies that will deal with these kinds of outages.

#Table of Content
{:toc}

## Outage at Amazon Web Services (AWS)
In case you missed it, last week AWS suffer a major outage in North America, resulting in taking down a chunk of the internet. Serval AWS services were expiring problems last week Wednesday early in the morning. Many organizations relying on AWS were impacted resulting in the degradation of their services which affected the customer experiences.

Sources: [The Verge](https://www.theverge.com/2020/11/25/21719396/amazon-web-services-aws-outage-down-internet){:target="_blank"} & [TechCrunch](https://techcrunch.com/2020/11/25/amazon-web-services-outage-takes-a-portion-of-the-internet-down-with-it/){:target="_blank"}

## How to deal with these outages
This is not just a single cloud service, each cloud provider is going to suffer outages, as this is not avoidable. Therefore, it is important to be prepared as an organization for these outages. No matter what type of organization, a strategy should be in place.
There are multiple options to consider as viable strategies.

* Multi-cloud strategy
* Multi-region strategy
* Hybrid cloud strategy

Before covering each induvial scenario, it is important to have some basics in place. These strategies are only successful when proper automation is in place, otherwise, some strategies might be not realistic.

Next to that proper procedures needs to be in place on how to determine outages and initiate failovers. This can be based on Service Level Agreements with the cloud provider. This should also contain at what point resources will be restored to the original state.

## Multi-cloud strategy
A multi-cloud strategy allows to leverage of different cloud services from various providers. The main reason for applying a multi-cloud strategy is to distribute resources and minimize the risk of downtime and data loss.
When the resources are distributed, an outage can still have an impact, so therefore it is important to have alternatives in place to ensure each resource cloud be partially run at the other cloud.

Alternative it is also possible to handle it as a disaster recovery, so in case of an outage, the services will failover to the alternative cloud.
This comes with a downside, have all resources in place does come with additional cost. As the main services of a cloud are the same, it does require specific knowledge of both cloud providers.

## Multi-region strategy
A safer option would be a multi-region strategy. As mentioned, the outage at AWS was only in one region. In this case, having an alternative region in place might have minimized the impact of the outages.

This is already common in large international organizations, as they have often branch offices and various countries. Applying this strategy will benefit the performance of the consumer as the resources can be physically closer to the consumer. In case of an outage, traffic can be redirected to the other region, but be aware, this will affect the consumer experience.

Staying with a single cloud provides does have the benefit of just the required knowledge of a single provider.

## Hybrid cloud strategy
When an organization wants to transition to cloud-only, this strategy does not apply.
When on-premises resources are available, a hybrid cloud strategy might be a very good option. This allows extending the on-premises resources in the combination of the cloud. In case of a cloud outage, the services can still rely on the on-premises resources, however, this does require to have the full capacity available on-premises.

Having on-premises hardware does require maintenance and hosing cost, but does provide the full dedication and control.

## Conclusion
Cloud provides will experience outages that might harm your services. In the case of AWS, a huge chunk of the internet was down due to a major outage in North America. It is important to be prepared for these kinds of outages to minimize the impact.

As learned, multiple strategies can be applied like a multi-cloud, multi-region, or hybrid cloud strategy.

Both multi and hybrid cloud strategies are the safest to ensure the impact is minimal. The biggest downside is the additional cost and in the case of hybrid the maintenance of physical hardware.
In case of the outage at AWS last week, a multi-region strategy would be sufficient, but when will be there an outage of the entire cloud provider?
In all cases, it is evaluating the cost compared to the risk. But in general, it is important to apply one of those strategies to ensure the services are available.

Additionally, it is very important to have a procedure in place to detect the outage of a cloud provider to initiate the failover. At what point is the failover going to initiate? This can be based on the service level agreement (SLA) of the cloud provider. And at what point are the resources going to be restored? These are important questions that should be in place before applying these kinds of strategies.

In the case of a multi-cloud strategy, the resource prices might be a failover driver. As resource prices at various cloud providers fluctuate it might help save cost.

The most important conclusion is to have proper automation in place to deploy the resources in one of those strategies. Based on data it should be possible to apply machine learning to detect these outages and recover automatically. But be aware automation platforms like Azure DevOps are hosted in the cloud and might be impacted by the outages.

<span>Photo by <a href="https://unsplash.com/@nicholasswanson?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Nicholas Swanson</a> on <a href="https://unsplash.com/s/photos/clouds?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Unsplash</a></span>
