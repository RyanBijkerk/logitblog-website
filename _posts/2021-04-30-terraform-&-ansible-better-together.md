---
layout: post
hidden: true
title: "Terraform & Ansible: Better Together"
authors: [ryan]
categories: ['As Code']
tags: [DevOps, EUC, Terraform, Ansible, 'As Code']
image: assets/images/posts/2021-04-30-terraform-&-ansible-better-together/terraform-&-ansible-better-together-feature-image.png
---
Over the last couple of months, a lot of my time has been defining technology stacks for multiple projects. Many organizations are in the middle of the DevOps transition and looking for guidance in selecting the right technology stack. This blog post will share some of my thoughts and dig a little bit deeper into Terraform & Ansible.

## What is Terraform?
Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions.

Configuration files describe to Terraform the components needed to run a single application or your entire datacenter. Terraform generates an execution plan describing what it will do to reach the desired state, and then executes it to build the described infrastructure. As the configuration changes, Terraform can determine what changed and create incremental execution plans which can be applied.

The infrastructure Terraform can manage includes low-level components such as compute instances, storage, and networking, as well as high-level components such as DNS entries, SaaS features, etc.

Source: [Introduction - Terraform by HashiCorp](https://www.terraform.io/intro/index.html){:target="_blank"}

## What is Ansible?
Ansible is a radically simple IT automation engine that automates cloud provisioning, configuration management, application deployment, intra-service orchestration, and many other IT needs.

Designed for multi-tier deployments since day one, Ansible models your IT infrastructure by describing how all of your systems inter-relate, rather than just managing one system at a time.

It uses no agents and no additional custom security infrastructure, so it's easy to deploy - and most importantly, it uses a very simple language (YAML, in the form of Ansible Playbooks) that allow you to describe your automation jobs in a way that approaches plain English.

Source: [How Ansible Works - Ansible](https://www.ansible.com/overview/how-ansible-works){:target="_blank"}

## Everything as code strategy
The term Infrastructure as Code (IaC) is becoming a standard, but from my perspective, this is just a small part of the bigger picture. From the DevOps thoughts, it is all about combining development and operation to minimize the gap between both worlds. By using a term as Infrastructure as Code the same gab is created. Infrastructure is the foundation of the services you want to offer, which is in most cases applications or access to data. In the end-user computing context, this can be a virtual desktop to deliver these applications.

"Everything as Code" is the correct term and from my perspective should be the standard. Everything as Code implies all components from infrastructure to the application delivery should be defined in code.

## Better together
With "everything as code" in mind, combining a Terraform with Ansible is a perfect combination to achieve this goal. Terraform allows the provision of the infrastructure where Ansible is there to install the applications and ensure the desired state is applied.

When selecting the toolset for a project the flexibility is always a key factor. In some cases, the environment might be still on-premises but in some cases, there is a desire to move the infrastructure to the cloud in the long term. As Terraform has multiple providers it empowers the flexibility to move to the cloud. 

Now in most cases and environments consist of multiple operating systems as Windows and Linux based. In a traditional environment, these are managed by dedicated teams. The Linux world is slowly invading the Windows side and more and more services are transitioning toward Linux. As Ansible can both manage Windows and Linux this is the ideal solution to ensure both can be managed by code.

In conclusion, both Terraform and Ansible empowers the flexibility to manage the infrastructure and application from a code perspective no matter the cloud provider and operating system.

## Executing Ansible from Terraform
Now to apply this in practice, lets cover an example. Terraform is capable to execute directly an Ansible playbook by using the local-exec provisioner. For example:

```hcl
provisioner "local-exec" {
    command = "ansible-playbook playbook-example.yaml -i ${vsphere_virtual_machine.vm_dhcp[count.index].default_ip_address}, -e ansible_user=${var.local_admin} -e ansible_password=${var.local_admin_password} -e ansible_connection=winrm -e ansible_winrm_server_cert_validation=ignore -e ansible_port=5985"
  }
```
The -i parameter stands for the inventory, this is the address of the machine which is in this case provided by Terraform. The -e parameter is the extra variables, this way you can provide the required parameters from the playbook. In this example the -e parameters are used to define the ansible_user and ansible_password for authentication and the ansible_connection for defining the WINRM protocol as the communication protocol, as this is a Windows-based machine.

Now as the Ansible playbook will be executed locally on the machine or DevOps agent, it must be a Linux-based operating system. This is because Ansible cannot be initiated from a Windows operating system.
See the Ansible documentation for more information: [Installing Ansible - Ansible Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html){:target="_blank"}.

## The drawback of this strategy and toolset
Now it is important to also understand the drawback of this strategy. At first, knowledge and mindset are big drawbacks of this strategy. This is also the biggest feedback received which was covered in a previous post.

Even though the toolset is available for years, in general to a lot of organizations it is new. This means teams need to get familiar with the tools but also the way of working in code. Luckily, there is a lot of content out there to get you started.

The choice for Ansible will force you to use a Linux-based operating system as the DevOps agent, as Ansible can only be executed from a Linux operating system. Nowadays PowerShell is supported on Linux, which should make this adoption a bit easier, but it is still a learning curve for those who never worked with Linux.

## Conclusion
Infrastructure as Code is just a small scope, so when starting a new project everything as code should be the defacto standard. By setting this as one of the principles is ensuring the set the correct expectation.

When selecting the toolset it is important to ensure to take the longer goals into account like the transition to a cloud provider. A solution like terraform also provides the possibility to adopt a multi-cloud strategy.

By combing a terraform and ansible together will empower everything in code as it can provision both infrastructure and the application.
Ansible playbook can directly be executed using the local-exec provisioner in Terraform ensuring the desired state is applied to the deployed infrastructure.

Hopefully, this post is helpful in your code journey and if you have any questions or remarks, please leave them in the comments below.

Photo by <a href="https://unsplash.com/@adigold1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Adi Goldstein</a> on <a href="https://unsplash.com/s/photos/together?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Unsplash</a>
  