---
layout: post
hidden: false
title: "Hands-on: Citrix Terraform provider"
authors: [ryan]
categories: ['Citrix']
tags: [Hashicorp, Terraform, Citrix, CVAD, DaaS,  Azure]
image: assets/images/posts/2023-11-11-hands-on-citrix-terraform-provider/hands-on-citrix-terraform-provider-feature-image.png
---
Last week, Citrix released a Tech preview of the [Citrix Terraform provider](https://registry.terraform.io/providers/citrix/citrix/latest){:target="_blank"}, which allows you to automate deployments and configurations of Citrix Virtual Apps & Desktops (CVAD) and Citrix Desktop as a Service (DaaS). This is very exciting for those doing infrastructure as code deployment, as this will help speed up the Citrix deployments. This post will explore the provider and its current capabilities.

## Tech Preview and Capabilities
As mentioned in the introduction, this release at the time of writing is in Tech Preview, which means this release is not officially supported yet. This Tech Preview is intended to be used in non-production or limited-production environments and to give customers an opportunity to share feedback.

The current release, [version 0.3.2](https://github.com/citrix/terraform-provider-citrix/releases){:target="_blank"} supports managing the following resources:
- Hypervisors
- Resource Pools
- Machine Catalogs
- Delivery Groups

Currently, the provider supports Citrix Virtual Apps & Desktops (CVAD) and Citrix Desktop as a Service (DaaS) solutions. More information can be found on the [Terraform website](https://registry.terraform.io/providers/citrix/citrix/latest){:target="_blank"} and the [GitHub repository](https://github.com/citrix/terraform-provider-citrix){:target="_blank"}.
## Getting started
To get started with the Citrix Terraform provider, begin with the provider configuration. Based on the [documentation](https://registry.terraform.io/providers/citrix/citrix/latest/docs){:target="_blank"}, two possibilities exist to authenticate to your Citrix environment. This can be either CVAD, an on-premises environment, or DaaS, the cloud-based solution.

> For CVAD, the Web Studio is required, available from version 2212 and later. Please be aware the only resources available at this time are DaaS only. At the time of writing, I could not connect successfully to the on-premises CVAD environment.

```hcl
terraform {
  required_providers {
    citrix = {
      source = "citrix/citrix"
      version = "0.3.2"
    }
  }
}

# Cloud Provider
provider "citrix" {
  customer_id   = ""
  client_id     = ""
  client_secret = ""
}

# On-Premise Provider
provider "citrix" {
  hostname      = "10.0.0.6"
  client_id     = "foo.local\\admin"
  client_secret = "foo"
}

```
### Zone
To create a hypervisor connection, machine catalog, or delivery group, a zone is required. The [zone](https://registry.terraform.io/providers/citrix/citrix/latest/docs/resources/daas_zone){:target="_blank"} is just the resource location that contains the cloud connectors. The ```citrix_daas_zone``` will not create the zone (resource location); therefore, it must be created beforehand. Once created in the Citrix DaaS portal, use the exact same name to have a successful deployment, like the example below.

![citrix-daas-resource-location]({{site.baseurl}}/assets/images/posts/2023-11-11-hands-on-citrix-terraform-provider/citrix-daas-resource-location.png)

```hcl
resource "citrix_daas_zone" "zone" {
  name = "go-euc"
}
```

### Hypervisor
Adding a [hypervisor connection](https://registry.terraform.io/providers/citrix/citrix/latest/docs/resources/daas_hypervisor){:target="_blank"} can be done using the ```citrix_daas_hypervisor``` resource. Based on the [documentation](https://registry.terraform.io/providers/citrix/citrix/latest/docs/resources/daas_hypervisor){:target="_blank"}, it can add an Azure, GPC, or AWS hypervisor connection. For this example, let's stick with the Azure hypervisor connection, which is the most familiar to me.

Additionally, there is the [```citrix_daas_hypervisor_resource_pool```](https://registry.terraform.io/providers/citrix/citrix/latest/docs/resources/daas_hypervisor_resource_pool){:target="_blank"}, which is the resource pool for the hypervisor connection. This requires an existing virtual network with a subnet in your Azure environment.

The following example will add a hypervisor connection and resource pool:

```hcl
resource "citrix_daas_hypervisor" "hypervisor" {
  name = "go-euc-azure"
  connection_type = "AzureRM"
  zone = citrix_daas_zone.zone.id
  active_directory_id = var.azure_tenant_id
  subscription_id = var.azure_subscription_id
  application_secret = var.azure_application_secret
  application_id = var.azure_application_id
}

resource "citrix_daas_hypervisor_resource_pool" "resource_pool" {
  name = "go-euc-pool"
  hypervisor = citrix_daas_hypervisor.hypervisor.id
  region = var.location #azurerm_resource_group.rg.location
  virtual_network_resource_group = azurerm_virtual_network.vnet.resource_group_name
  virtual_network = azurerm_virtual_network.vnet.name
  subnets = [azurerm_subnet.subnet.name]
}
```

![citrix-daas-hosting-connection]({{site.baseurl}}/assets/images/posts/2023-11-11-hands-on-citrix-terraform-provider/citrix-daas-hosting-connection.png)

### Machine catalog and delivery group
The next phase would be creating a machine catalog and delivery group. As there are some prerequisites to creating the machine catalog, I did not manage yet to have a working example. This requires having a running domain, a master VM with the VDA installed, and active cloud connectors. Due to limited time, I have decided to postpone this to a later stage. However, the [documentation does provide an example](https://registry.terraform.io/providers/citrix/citrix/latest/docs/resources/daas_machine_catalog){:target="_blank"}.

## GitHub example
The code used in this example is published on [GitHub](https://github.com/RyanBijkerk/terraform-citrix){:target="_blank"}, including the Azure requirements. Store the following information in the terraform.tfvars:

| Variable | Value |
|:--- | :----- |
| customer_id | Citrix Customer ID |
| client_id | Citrix Client ID |
| client_secret | Citrix Client Secret |
| name | Name of the resources |
| azure_tenant_id | Azure Tenant ID |
| azure_subscription_id | Azure Subscription ID|
| azure_application_id | Azure Application ID |
| azure_application_secret | Azure Application Secret |

<br>
Run the following command in the correct working directory:

```cmd
terraform init
terraform apply
```

## Conclusion
It is very exciting that Citrix is creating a Terraform provider, as this will speed up the automated deployments. The provider is a highly requested feature and it is awesome to see Citrix is putting this effort in. At this stage, the tech preview is limited to DaaS only but it is expected to see frequent updates with more functionality. So, it's definitely something to keep an eye out for on the GitHub release page.

I hope this post gave a brief overview of how to get started with the Citrix Terraform provider, and if you have any questions or comments, please leave them below.

Photo by [Lenny Kuhne](https://unsplash.com/@lennykuhne?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"} on [Unsplash](https://unsplash.com/photos/gray-vehicle-being-fixed-inside-factory-using-robot-machines-jHZ70nRk7Ns?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"}
