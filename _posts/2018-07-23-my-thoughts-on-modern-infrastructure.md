---
layout: post
title: My thoughts on Modern Infrastructure
authors: [ryan]
comments: true
categories: [Containers]
tags: [Citrix, Containers, Containers, Docker, Microsoft, Modern Infrastructure, VMware]
image: assets/images/posts/2018-07-23-my-thoughts-on-modern-infrastructure/my-thoughts-on-modern-infrastructure-feature-image.png
---
Modern infrastructure, is it valuable or just another buzzword? Organizations like Microsoft are working hard on this type of modern infrastructure with Remote Desktop services. But what is modern infrastructure? In this blog post, I will explain the modern infrastructure and share my thoughts about this topic.
<h2>Modern applications</h2>
The cloud and software as a service have changed the perspective on applications. Swiping a credit card to get access to an app is common nowadays. When having new employees, no problem, just add them to the existing subscription. Also from a user perspective, the expectations of an application have also changed. We expect it is always available from any place and at any time without any performance degradation.

Containers technology is very common in modern development organizations. Containers allow developers to have a predictable deployment where each component is isolated and scalable. Also, techniques like Microservices allows developers to be more agile with the updates and scalability of their applications.
<h2>Modern infrastructure like Microsoft RDMi</h2>
Somewhere last year Microsoft introduced Remote Desktop Modern infrastructure (RDMi). This is Microsoft's hybrid solution to provide remote desktop services. Part of the infrastructure is hosted in the cloud where the session hosts can run on-premises. This is not new, as both Citrix and VMware are also offering a hybrid solution. If you want to know more about Microsoft RDmi, here are a couple of interesting articles.

<strong>Link:</strong> <a href="https://www.brianmadden.com/opinion/RDmi-is-a-milestone-for-the-future-of-RDS-Heres-how-it-works" target="_blank" rel="noopener">RDmi is a milestone for the future of RDS. Here’s how it works</a>
<strong>Link:</strong> <a href="https://www.petri.com/remote-desktop-modern-infrastructure" target="_blank" rel="noopener">What Is Remote Desktop Modern Infrastructure?</a>

When offering a hybrid solution some of the components are not running in the domain. This "modern" style required vendors to re-architecture their solutions so it would be compatible in a cloud or hybrid scenario. From my perspective, modern infrastructure solutions run isolated, stand-alone and are scalable.
<h2>From hybrid or cloud to containers</h2>
Building a traditional virtual desktop environment on-premises takes some resources and time. You require multiple servers for the different components and its common to have them high available. Modern infrastructure can resolve the hassle of building this kind of environments. I really hope containers will be the default solution to host infrastructure components. With the current deployments, it's not possible as there is a tight integration with a domain. But as modern infrastructure is isolated and stand-alone this will be resolved. As Docker uses a public repository the images will be available so you can just focus on the configuration.
<h2>Scalability and high availability</h2>
One of the huge benefits of containers is scalability. When using an orchestration solution like Kubernetes this is all taken care of. Kubernetes is capable to scale up and down containers based on usage and demand. In case of a fault, when the application or infrastructure component crashes, it can kill the container and spawn a new one. I heard a scenario during a presentation where the first couple of days the developers did not notice they had a memory leak in their software. Kubernetes detected there was a high demand from the specific container, killed it and spawned a new one. I really hope this kind of technology will enter the IT Pro space as it can help make our jobs easier.
<h2>Conclusion</h2>
From my perspective, modern infrastructure is not a buzzword. It going to take some time but I really hope modern infrastructure will be the new way for cloud and on-premises. Building a virtual desktop environment is all about the applications. Why spending time building the infrastructure? Container technology is capable to deploy an entire environment in a couple of minutes. Why not deploy the virtual desktop infrastructure in containers? Using an orchestration solution like Kuberneters will make it scalable and high available. What do you think about this topic? Share your thoughts in the comments below!

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/E0f9iLMTuVQ?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Scott Webb</a> on <a href="https://unsplash.com/search/photos/modern?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
