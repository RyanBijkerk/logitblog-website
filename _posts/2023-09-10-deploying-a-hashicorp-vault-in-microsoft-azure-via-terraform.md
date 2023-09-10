---
layout: post
hidden: true
title: "Deploying a Hashicorp Vault in Microsoft Azure via Terraform"
authors: [ryan]
categories: ['Vault']
tags: [Hashicorp, Vault, Terraform, Azure]
image: assets/images/posts/2023-09-11-deploying-a-hashicorp-vault-in-microsoft-azure-via-terraform/deploying-a-hashicorp-vault-in-microsoft-azure-via-terraform-feature-image.png
---
When implementing infrastructure in the cloud, it is considered a best practice to employ a vault solution for securely storing credentials. HashiCorp offers such a solution known as Vault, which can be deployed via a Docker container. This blog post will demonstrate the process of deploying HashiCorp Vault on Microsoft Azure using Terraform.

## What is Hashicorp Vault?
Before delving into the details, it is important to grasp the true essence of what Vault represents.

HashiCorp Vault is an identity-based secrets and encryption management system. A _secret_ is anything that you want to tightly control access to, such as API encryption keys, passwords, and certificates. Vault provides encryption services that are gated by authentication and authorization methods. Using Vault’s UI, CLI, or HTTP API, access to secrets and other sensitive data can be securely stored and managed, tightly controlled (restricted), and auditable.

Most enterprises today have credentials sprawled across their organisations. Passwords, API keys, and credentials are stored in plain text, app source code, config files, and other locations. Because these credentials live everywhere, the sprawl can make it difficult and daunting to really know who has access and authorization to what. Having credentials in plain text also increases the potential for malicious attacks, both by internal and external attackers.

Vault was designed with these challenges in mind. Vault takes all of these credentials and centralizes them so that they are defined in one location, which reduces unwanted exposure to credentials. But Vault takes it a few steps further by making sure users, apps, and systems are authenticated and explicitly authorized to access resources, while also providing an audit trail that captures and preserves a history of clients' actions.

Source: [Introduction - Vault - HashiCorp Developer](https://developer.hashicorp.com/vault/docs/what-is-vault){:target="_blank"}

## Why using a Hashicorp Vault in Microsoft Azure?
What an excellent question, because, as you may already be aware, Azure does offer a built-in solution known as Azure KeyVault. However, in this particular scenario, the choice to utilize HashiCorp Vault is rooted in our multi-cloud strategy. This example draws inspiration from the GO-EUC Infrastructure as Code project, which revolves around deploying test environments across various cloud providers and on-premises setups. All infrastructure deployments are orchestrated using HashiCorp Terraform, and the desired state is configured using Ansible.

As you might be aware, Terraform is cloud-specific, meaning deployment targets are tailored to a specific platform. This, however, isn't the case with Ansible. Opting for HashiCorp allows us to seamlessly integrate a single Vault solution into Ansible, making it possible to reuse all Ansible playbooks across different platforms. This streamlines operations and reduces overhead significantly.

## Terraform configuration
There isn't a preconfigured Azure offering for deploying HashiCorp Vault through a gallery item. Fortunately, HashiCorp provides a Docker image for Vault, which you can find [here](https://hub.docker.com/r/hashicorp/vault){:target="_blank"} on Docker Hub. This allows us to employ a container instance to host Vault within Azure.

By default, when utilizing the Docker image, it operates in developer mode, essentially running in-memory. Consequently, any added secrets are not stored persistently. Therefore, to transition to a production-ready setup, it becomes necessary to allocate storage for storing both configuration data and Vault files.

### Vault config
Let's begin by discussing the Vault configuration file, which serves as the foundation for Vault operation. Here's a simple example where TLS is disabled. It's important to note that in a production environment, it is highly recommended to enable TLS and provide a valid certificate for enhanced security.

```
storage "file" {
  path = "/etc/vault/file"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  cluster_address = "0.0.0.0:8201"
  tls_disable = true
}

api_addr = "http://0.0.0.0:8200"
ui = true
disable_mlock = true
```

More information about the configuration file can be found here: [Server Configuration - Vault - HashiCorp Developer](https://developer.hashicorp.com/vault/docs/configuration){:target="_blank"}
### Providers
Now, let's break down the Terraform configuration, organized into individual files. Since this deployment is specifically for Azure, the `hashicorp/azurerm` module is essential. Additionally, we employ the `hashicorp/random` module to generate a random name for the storage account.

```
terraform {

  required_version = ">= 1.2"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">=2.9"
    }

    random = {
      source = "hashicorp/random"
      version = ">=3.4"
    }
  }
}

provider "azurerm" {
  features {}

  subscription_id = var.azure_subscription_id
  client_id       = var.azure_client_id
  client_secret   = var.azure_client_secret
  tenant_id       = var.azure_tenant_id
}

```
### Resource group
As always, we start with the resource group that encompasses all the various components. The region is determined by a variable specified later in the configuration.

```
resource "azurerm_resource_group" "rg" {
  name     = "golab-backend"
  location = var.azure_region
}
```
### Virtual Network
To establish connectivity to the container instance and, consequently, to Vault, a virtual network is a necessity. In this example, we utilize two distinct subnets: one for management purposes and another dedicated exclusively to the container instance.

```
resource "azurerm_virtual_network" "vnet" {
  name          = "golab-vnet"
  address_space = ["10.0.0.0/16"]
  dns_servers   = [cidrhost("10.0.200.0/24", 10)]

  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "mgmt" {
  name                                           = "golab-mgmt"
  resource_group_name                            = azurerm_resource_group.rg.name
  virtual_network_name                           = azurerm_virtual_network.vnet.name
  address_prefixes                               = ["10.0.200.0/24"]
}

resource "azurerm_subnet" "docker" {
  name                 = "golab-docker"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.124.0/24"]

  delegation {
    name = "dl-docker"

    service_delegation {
      name    = "Microsoft.ContainerInstance/containerGroups"
      actions = ["Microsoft.Network/virtualNetworks/subnets/join/action", "Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action"]
    }
  }

  lifecycle {
    ignore_changes = [
      delegation
    ]
  }
}

resource "azurerm_network_profile" "docker" {
  name                = "np-docker"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  container_network_interface {
    name = "nic-docker"

    ip_configuration {
      name      = "ip-docker"
      subnet_id = azurerm_subnet.docker.id
    }
  }
}
```
### Storage account
To furnish the configuration file to the container instance while also ensuring Vault's persistence, we rely on a storage account. Because the storage account name must be unique, we utilize the `random_integer` to generate a unique value. Two directories will be established: `config`, where the `config.hcl` file will be stored, and `file`, which serves as the default location for storing all Vault files.

```
resource "random_integer" "vault" {
  min = 10000
  max = 99999
}

resource "azurerm_storage_account" "vault" {
  name                     = "vault${random_integer.vault.result}"
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_share" "vault" {
  name                 = "vault"
  storage_account_name = azurerm_storage_account.vault.name
  quota                = 50

  lifecycle {
    ignore_changes = all
  }
}

resource "azurerm_storage_share_directory" "config-dir" {
  name                 = "config"
  share_name           = azurerm_storage_share.vault.name
  storage_account_name = azurerm_storage_account.vault.name

  lifecycle {
    ignore_changes = all
  }
}

resource "azurerm_storage_share_directory" "file-dir" {
  name                 = "file"
  share_name           = azurerm_storage_share.vault.name
  storage_account_name = azurerm_storage_account.vault.name

  lifecycle {
    ignore_changes = all
  }
}

resource "azurerm_storage_share_file" "file" {
  name             = "config.hcl"
  path             = azurerm_storage_share_directory.config-dir.name
  storage_share_id = azurerm_storage_share.vault.id
  source           = "./config/vault/config.hcl"

  lifecycle {
    ignore_changes = all
  }
}
```
### Container instance
The container instance will utilize the public and latest available version of the `hashicorp/vault` image from DockerHub. Depending on the resource requirements, it's possible to allocate more CPU and memory. A volume mount will be configured to map to the storage account where both the configuration and persistent files are stored. The command issued will run Vault in production mode, with the mapping to the specified configuration.

The default port, 8200, will also be mapped. Notably, the `ip_address_type` is set to private, meaning that Vault is only accessible from within the virtual network. It's worth mentioning that this example does not include a virtual machine, so adding one would be contingent on your specific use-case.

```
resource "azurerm_container_group" "docker" {
  name = "golab-docker"

  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_address_type    = "Private"
  network_profile_id = azurerm_network_profile.docker.id
  os_type            = "Linux"

  container {
    name   = "vault"
    image  = "hashicorp/vault:latest"
    cpu    = "1"
    memory = "2"

    commands = [ "/bin/sh", "-c", "vault server -config=/etc/vault/config/config.hcl" ]

    ports {
      port     = 8200
      protocol = "TCP"
    }

    volume {
      name       = "vault"
      mount_path = "/etc/vault"
      read_only  = false
      share_name = azurerm_storage_share.vault.name

      storage_account_name = azurerm_storage_account.vault.name
      storage_account_key  = azurerm_storage_account.vault.primary_access_key

    }
  }
}
```

### Variables
Lastly, let's cover the essential variables required for this configuration.

```
variable "azure_subscription_id" {
  type      = string
  sensitive = true
}

variable "azure_client_id" {
  type      = string
  sensitive = true
}

variable "azure_client_secret" {
  type      = string
  sensitive = true
}

variable "azure_tenant_id" {
  type      = string
  sensitive = true
}

variable "azure_region" {
  type    = string
  default = "westeurope"
}
```

You can find the complete code sample on my [GitHub page](https://github.com/RyanBijkerk/vault-in-azure){:target="_blank"}.

## End result and conclusion
When you apply the configuration from this example, it will result in the following deployment.

```
terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_container_group.docker will be created
  + resource "azurerm_container_group" "docker" {
      + dns_name_label_reuse_policy = "Unsecure"
      + exposed_port                = (known after apply)
      + fqdn                        = (known after apply)
      + id                          = (known after apply)
      + ip_address                  = (known after apply)
      + ip_address_type             = "Private"
      + location                    = "westeurope"
      + name                        = "golab-docker"
      + network_profile_id          = (known after apply)
      + os_type                     = "Linux"
      + resource_group_name         = "golab-backend"
      + restart_policy              = "Always"
      + sku                         = "Standard"

      + container {
          + commands = [
              + "/bin/sh",
              + "-c",
              + "vault server -config=/etc/vault/config/config.hcl",
            ]
          + cpu      = 1
          + image    = "hashicorp/vault:latest"
          + memory   = 2
          + name     = "vault"

          + ports {
              + port     = 8200
              + protocol = "TCP"
            }

          + volume {
              + empty_dir            = false
              + mount_path           = "/etc/vault"
              + name                 = "vault"
              + read_only            = false
              + share_name           = "vault"
              + storage_account_key  = (sensitive value)
              + storage_account_name = (known after apply)
            }
        }
    }

  # azurerm_network_profile.docker will be created
  + resource "azurerm_network_profile" "docker" {
      + container_network_interface_ids = (known after apply)
      + id                              = (known after apply)
      + location                        = "westeurope"
      + name                            = "np-docker"
      + resource_group_name             = "golab-backend"

      + container_network_interface {
          + name = "nic-docker"

          + ip_configuration {
              + name      = "ip-docker"
              + subnet_id = (known after apply)
            }
        }
    }

  # azurerm_resource_group.rg will be created
  + resource "azurerm_resource_group" "rg" {
      + id       = (known after apply)
      + location = "westeurope"
      + name     = "golab-backend"
    }

  # azurerm_storage_account.vault will be created
  + resource "azurerm_storage_account" "vault" {
      + access_tier                       = (known after apply)
      + account_kind                      = "StorageV2"
      + account_replication_type          = "LRS"
      + account_tier                      = "Standard"
      + allow_nested_items_to_be_public   = true
      + cross_tenant_replication_enabled  = true
      + default_to_oauth_authentication   = false
      + enable_https_traffic_only         = true
      + id                                = (known after apply)
      + infrastructure_encryption_enabled = false
      + is_hns_enabled                    = false
      + large_file_share_enabled          = (known after apply)
      + location                          = "westeurope"
      + min_tls_version                   = "TLS1_2"
      + name                              = (known after apply)
      + nfsv3_enabled                     = false
      + primary_access_key                = (sensitive value)
      + primary_blob_connection_string    = (sensitive value)
      + primary_blob_endpoint             = (known after apply)
      + primary_blob_host                 = (known after apply)
      + primary_connection_string         = (sensitive value)
      + primary_dfs_endpoint              = (known after apply)
      + primary_dfs_host                  = (known after apply)
      + primary_file_endpoint             = (known after apply)
      + primary_file_host                 = (known after apply)
      + primary_location                  = (known after apply)
      + primary_queue_endpoint            = (known after apply)
      + primary_queue_host                = (known after apply)
      + primary_table_endpoint            = (known after apply)
      + primary_table_host                = (known after apply)
      + primary_web_endpoint              = (known after apply)
      + primary_web_host                  = (known after apply)
      + public_network_access_enabled     = true
      + queue_encryption_key_type         = "Service"
      + resource_group_name               = "golab-backend"
      + secondary_access_key              = (sensitive value)
      + secondary_blob_connection_string  = (sensitive value)
      + secondary_blob_endpoint           = (known after apply)
      + secondary_blob_host               = (known after apply)
      + secondary_connection_string       = (sensitive value)
      + secondary_dfs_endpoint            = (known after apply)
      + secondary_dfs_host                = (known after apply)
      + secondary_file_endpoint           = (known after apply)
      + secondary_file_host               = (known after apply)
      + secondary_location                = (known after apply)
      + secondary_queue_endpoint          = (known after apply)
      + secondary_queue_host              = (known after apply)
      + secondary_table_endpoint          = (known after apply)
      + secondary_table_host              = (known after apply)
      + secondary_web_endpoint            = (known after apply)
      + secondary_web_host                = (known after apply)
      + sftp_enabled                      = false
      + shared_access_key_enabled         = true
      + table_encryption_key_type         = "Service"

      + blob_properties {
          + change_feed_enabled           = (known after apply)
          + change_feed_retention_in_days = (known after apply)
          + default_service_version       = (known after apply)
          + last_access_time_enabled      = (known after apply)
          + versioning_enabled            = (known after apply)

          + container_delete_retention_policy {
              + days = (known after apply)
            }

          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + delete_retention_policy {
              + days = (known after apply)
            }

          + restore_policy {
              + days = (known after apply)
            }
        }

      + network_rules {
          + bypass                     = (known after apply)
          + default_action             = (known after apply)
          + ip_rules                   = (known after apply)
          + virtual_network_subnet_ids = (known after apply)

          + private_link_access {
              + endpoint_resource_id = (known after apply)
              + endpoint_tenant_id   = (known after apply)
            }
        }

      + queue_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + hour_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }

          + logging {
              + delete                = (known after apply)
              + read                  = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
              + write                 = (known after apply)
            }

          + minute_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }
        }

      + routing {
          + choice                      = (known after apply)
          + publish_internet_endpoints  = (known after apply)
          + publish_microsoft_endpoints = (known after apply)
        }

      + share_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + retention_policy {
              + days = (known after apply)
            }

          + smb {
              + authentication_types            = (known after apply)
              + channel_encryption_type         = (known after apply)
              + kerberos_ticket_encryption_type = (known after apply)
              + multichannel_enabled            = (known after apply)
              + versions                        = (known after apply)
            }
        }
    }

  # azurerm_storage_share.vault will be created
  + resource "azurerm_storage_share" "vault" {
      + access_tier          = (known after apply)
      + enabled_protocol     = "SMB"
      + id                   = (known after apply)
      + metadata             = (known after apply)
      + name                 = "vault"
      + quota                = 50
      + resource_manager_id  = (known after apply)
      + storage_account_name = (known after apply)
      + url                  = (known after apply)
    }

  # azurerm_storage_share_directory.config-dir will be created
  + resource "azurerm_storage_share_directory" "config-dir" {
      + id                   = (known after apply)
      + name                 = "config"
      + share_name           = "vault"
      + storage_account_name = (known after apply)
    }

  # azurerm_storage_share_directory.file-dir will be created
  + resource "azurerm_storage_share_directory" "file-dir" {
      + id                   = (known after apply)
      + name                 = "file"
      + share_name           = "vault"
      + storage_account_name = (known after apply)
    }

  # azurerm_storage_share_file.file will be created
  + resource "azurerm_storage_share_file" "file" {
      + content_length   = (known after apply)
      + content_type     = "application/octet-stream"
      + id               = (known after apply)
      + name             = "config.hcl"
      + path             = "config"
      + source           = "./config/vault/config.hcl"
      + storage_share_id = (known after apply)
    }

  # azurerm_subnet.docker will be created
  + resource "azurerm_subnet" "docker" {
      + address_prefixes                               = [
          + "10.0.124.0/24",
        ]
      + enforce_private_link_endpoint_network_policies = (known after apply)
      + enforce_private_link_service_network_policies  = (known after apply)
      + id                                             = (known after apply)
      + name                                           = "golab-docker"
      + private_endpoint_network_policies_enabled      = (known after apply)
      + private_link_service_network_policies_enabled  = (known after apply)
      + resource_group_name                            = "golab-backend"
      + virtual_network_name                           = "golab-vnet"

      + delegation {
          + name = "dl-docker"

          + service_delegation {
              + actions = [
                  + "Microsoft.Network/virtualNetworks/subnets/join/action",
                  + "Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action",
                ]
              + name    = "Microsoft.ContainerInstance/containerGroups"
            }
        }
    }

  # azurerm_subnet.mgmt will be created
  + resource "azurerm_subnet" "mgmt" {
      + address_prefixes                               = [
          + "10.0.200.0/24",
        ]
      + enforce_private_link_endpoint_network_policies = (known after apply)
      + enforce_private_link_service_network_policies  = (known after apply)
      + id                                             = (known after apply)
      + name                                           = "golab-mgmt"
      + private_endpoint_network_policies_enabled      = (known after apply)
      + private_link_service_network_policies_enabled  = (known after apply)
      + resource_group_name                            = "golab-backend"
      + virtual_network_name                           = "golab-vnet"
    }

  # azurerm_virtual_network.vnet will be created
  + resource "azurerm_virtual_network" "vnet" {
      + address_space       = [
          + "10.0.0.0/16",
        ]
      + dns_servers         = [
          + "10.0.200.10",
        ]
      + guid                = (known after apply)
      + id                  = (known after apply)
      + location            = "westeurope"
      + name                = "golab-vnet"
      + resource_group_name = "golab-backend"
      + subnet              = (known after apply)
    }

  # random_integer.vault will be created
  + resource "random_integer" "vault" {
      + id     = (known after apply)
      + max    = 99999
      + min    = 10000
      + result = (known after apply)
    }

Plan: 12 to add, 0 to change, 0 to destroy.
```

![azure-backend-deployment]({{site.baseurl}}/assets/images/posts/2023-09-11-deploying-a-hashicorp-vault-in-microsoft-azure-via-terraform/azure-backend-deployment.png)

As previously mentioned, the Vault is only accessible from within the virtual network.

![hashicorp-vault-ui]({{site.baseurl}}/assets/images/posts/2023-09-11-deploying-a-hashicorp-vault-in-microsoft-azure-via-terraform/hashicorp-vault-ui.png)

When selecting a Vault solution, it is of paramount importance to have a clear understanding of the long-term goals and assess whether the chosen solution aligns with those objectives. In the case of GO-EUC, the utilization of multiple cloud solutions depends on research requirements. To support all deployment types and facilitate the reuse of Ansible configurations, HashiCorp Vault emerges as the most suitable choice. It can seamlessly operate on various platforms, including on-premises setups, enabling a consistent method for storing and retrieving secrets. Furthermore, HashiCorp Vault is an open-source Docker instance, making it freely accessible for use.

Additionally, HashiCorp offers an enterprise solution where the Vault is hosted for you. For more information, please visit the HashiCorp website.

If you have any questions or comments, please feel free to leave them below.

Photo by [Brock Wegner](https://unsplash.com/@isthatbrock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/photos/3ROwc3JSjCk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}
