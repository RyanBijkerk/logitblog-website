---
layout: post
hidden: false
title: "Terraform 101 tip: element function"
authors: [ryan]
categories: ['Terraform']
tags: [Terraform, IaC, Code]
image: assets/images/posts/2021-08-27-terraform-101-tips-element-function/terraform-101-tips-element-function-feature-image.png
---
In the last couple of months, my day-to-day work involves a lot working with Terraform. Now sometimes there are features or functions which are not described clearly. Therefore introducing 101 tips, a series sharing some tips that might help you. The first blog post of the series will be covering the function element.

## What is Terraform?
For those who are not familiar with Terraform, Terraform is an infrastructure as code (IaC) tool that allows you to build, change, and version infrastructure safely and efficiently. This includes low-level components such as compute instances, storage, and networking, as well as high-level components such as DNS entries, SaaS features, etc. Terraform can manage both existing service providers and custom in-house solutions.

<iframe width="800" height="450" src="https://www.youtube.com/embed/h970ZBgKINg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Key Features
  * Infrastructure as Code
  * Execution Plans
  * Resource Graph
  * Change Automation

More information can be found here: [Introduction - Terraform by HashiCorp](https://www.terraform.io/intro/index.html){:target="_blank"}.

## Function: element
Let's start with covering the function documentation. The description of the function is as followed.

Element retrieves a single element from a list.

```
element(list, index)
```

The index is zero-based. This function produces an error if used with an empty list. The index must be a non-negative integer.
Use the built-in index syntax list[index] in most cases. Use this function only for the special additional "wrap-around" behavior described below.
Source: [element - Functions - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/language/functions/element.html){:target="_blank"}.

Now, this is one of the examples that is shown in the function documentation:

```
> element(["a", "b", "c"], 1)
b
```

The function will select the property of an array based on the index number. In most programming languages when reaching a number that is out of the range of the array, you will receive an exception. But the element function will continue from the first item in the array. This will result in the following behavior.

| Index | Array |
| :-------  | :----- | 
| 0 | a |
| 1 | b |
| 2 | c | 
| 3 | a |
| 4 | b |
| 5 | c | 
| 6 | a |

## Use case example
To fully understand the function and how to use it let's cover this by using a use case. 

Let's say it is required to have 6 databases distributed over 3 different locations. This can easily be done using the element function.
The example is based on the [azurerm_mariadb_server](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/mariadb_server){:target="_blank"} resource.


```
provider "azurerm" {
  features {}
}

locals {
  locations =["West Europe", "East US", "West US"]
}

resource "azurerm_resource_group" "example" {
  count    = 3
  name     = "example-resources"
  location = element(local.locations, count.index)
}

resource "azurerm_mariadb_server" "example" {
  count               = 6
  name                = "example-mariadb-server-${count.index + 1}"
  location            = element(azurerm_resource_group.example, count.index).location
  resource_group_name = element(azurerm_resource_group.example, count.index).name

  administrator_login          = "mariadbadmin"
  administrator_login_password = "H@Sh1CoR3!"

  sku_name   = "B_Gen5_2"
  storage_mb = 5120
  version    = "10.2"

  auto_grow_enabled             = true
  backup_retention_days         = 7
  geo_redundant_backup_enabled  = false
  public_network_access_enabled = false
  ssl_enforcement_enabled       = true
}

```

The locals are used to create an array with the different locations. When creating the required resource group, the location is collected from the location array by using ```element(local.locations, count.index)```.  

To distribute the databases over these locations the element function is again used to select the correct resource group.

```element(azurerm_resource_group.example, count.index).location```

This example shows you can both select a single item from an array but also other properties, in this case from the created resource groups.

When planning the example it will result in creating 9 resources:

3 resource groups:
```
  # azurerm_resource_group.example[0] will be created
  + resource "azurerm_resource_group" "example" {
      + id       = (known after apply)
      + location = "westeurope"
      + name     = "example-resources-1"
    }

  # azurerm_resource_group.example[1] will be created
  + resource "azurerm_resource_group" "example" {
      + id       = (known after apply)
      + location = "eastus"
      + name     = "example-resources-2"
    }

  # azurerm_resource_group.example[2] will be created
  + resource "azurerm_resource_group" "example" {
      + id       = (known after apply)
      + location = "westus"
      + name     = "example-resources-3"
    }
```
6 databases
```
  # azurerm_mariadb_server.example[0] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "westeurope"
      + name                          = "example-mariadb-server-1"
    }

  # azurerm_mariadb_server.example[1] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "eastus"
      + name                          = "example-mariadb-server-2"
    }

  # azurerm_mariadb_server.example[2] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "westus"
      + name                          = "example-mariadb-server-3"
    }

  # azurerm_mariadb_server.example[3] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "westeurope"
      + name                          = "example-mariadb-server-4"
    }

  # azurerm_mariadb_server.example[4] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "eastus"
      + name                          = "example-mariadb-server-5"
    }

  # azurerm_mariadb_server.example[5] will be created
  + resource "azurerm_mariadb_server" "example" {
      + location                      = "westus"
      + name                          = "example-mariadb-server-6"
    }
```

## Conclusion
Sometimes you end up in a situation where you will think, how are we going to do this? From my experience, I often encounter this, especially when you are not 100% familiar with the language and all the functions.

The element function is very powerful which can help you with selecting a single element or iterating through multiple options. As shown above, is a nice example to distribute those databases over the various locations and still using 1 definition. This shows the true power of the element function.

I hope this Terraform 101 tip was helpful and if you have any suggestions please let me know in the comments below.

Photo by [Fakurian Design](https://unsplash.com/@fakurian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/s/photos/modern?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}