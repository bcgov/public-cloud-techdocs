# Infrastructure-as-Code (IaC) and CI/CD

Last updated: **{{ git_revision_date_localized }}**

## Overview

!!! note "Terraform and resource tags"
    When using the [Azure Verified Module (AVM) for CICD Agents and Runners](https://github.com/Azure/terraform-azurerm-avm-ptn-cicd-agents-and-runners), the [Azure Verified Module for Managed DevOps Pools](https://github.com/Azure/terraform-azurerm-avm-res-devopsinfrastructure-pool) or any other modules, Terraform may show that it will remove certain tags from resources. This is because the module is not aware of the tags that are set on the resources. If you want to keep the tags, you can add a `lifecycle` block with the [ignore_changes](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle#ignore_changes) feature, to the resource in your Terraform code to ignore the tags.

## Terraform

### Using Terraform to create subnets

If you are using Terraform to create your infrastructure, in particular the subnets within your assigned Virtual Network, please be aware of the following challenge.

The Azure Landing Zones have an Azure Policy implemented that requires every subnet to have an associated Network Security Group (NSG) for security controls compliance. The challenge with this is that Terraform doesn't support the creation of subnets with an associated NSG in a _single step_.

Therefore, instead of using the `azurerm_subnet` resource to create subnets, you must use the `azapi_resource` resource from the [AzAPI Terraform Provider](https://registry.terraform.io/providers/Azure/azapi/latest/docs). This resource allows you to create subnets with an associated NSG in a single step.

!!! abstract "AzAPI resource provider"
    You need to use the `azapi_resource` resource, because you are updating an existing Virtual Network (VNet) resource with a new subnet (and associated Network Security Group).

**Example code:**

```terraform
resource "azapi_resource" "subnets" {
  type = "Microsoft.Network/virtualNetworks/subnets@2024-05-01"

  name      = "SubnetName"
  parent_id = data.azurerm_virtual_network.vnet.id
  # Note: Discovered the `locks` attribute for AzAPI from the following GitHub Issue: https://github.com/Azure/terraform-provider-azapi/issues/503
  # A list of ARM resource IDs which are used to avoid create/modify/delete azapi resources at the same time.
  locks = [
    data.azurerm_virtual_network.vnet.id
  ]

  # It's not necessary to use the `jsonencode` function to encode the HCL object to JSON, just use the HCL object directly
  body = {
    properties = {
      networkSecurityGroup = {
        id = azurerm_network_security_group.id
      }
    }
  }

  response_export_values = ["*"]
}
```

For further details about this limitation, please refer to the following GitHub Issue: [Example of using the Subnet Association resources with Azure Policy](https://github.com/hashicorp/terraform-provider-azurerm/issues/9022).

### Using Terraform to create private endpoints

If you are using Terraform to create your infrastructure, in particular Private Endpoints within your assigned Virtual Network, please be aware of the following challenge.

After the Private Endpoint is created, the automation (via Azure Policy) within the Landing Zones will automatically associate the Private Endpoint with the appropriate Private DNS Zone, and create the necessary DNS records (see [Private Endpoints and DNS](./be-mindful.md#private-endpoints-and-dns) for more details).

However, the next time you run `terraform plan` or `terraform apply`, Terraform will detect that this change has occurred outside of your specific Terraform code, and will attempt to **remove** the association between the Private Endpoint and the Private DNS Zone. This will cause issues with resolving your resources via the Private Endpoint.

**Example `terraform plan` output:**

```terraform
  ~ resource "azurerm_private_endpoint" "this" {
        id                            = "/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/caf-ghr/providers/Microsoft.Network/privateEndpoints/pe-acrghr"
        name                          = "pe-acrghr"

      - private_dns_zone_group {
          - id                   = "/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/caf-ghr/providers/Microsoft.Network/privateEndpoints/pe-acrghr/privateDnsZoneGroups/deployedByPolicy" -> null
          - name                 = "deployedByPolicy" -> null
          - private_dns_zone_ids = [
              - "/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/bcgov-managed-lz-forge-dns/providers/Microsoft.Network/privateDnsZones/privatelink.azurecr.io",
            ] -> null
        }
    }
```

While the Azure Policy **_should_** automatically re-associate the Private Endpoint with the Private DNS Zone, it is recommended to add a `lifecycle` block with the [ignore_changes](https://www.terraform.io/docs/language/meta-arguments/lifecycle.html#ignore_changes) feature on the `private_dns_zone_group` resource property in your Terraform code, to ignore the changes that are applied through the Azure Policy.

**Example `terraform lifecycle ignore_changes`:**

```terraform
resource "azurerm_private_endpoint" "example" {
  name                = "example-endpoint"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  subnet_id           = azurerm_subnet.example.id

  private_service_connection {
    name                           = "example-privateserviceconnection"
    private_connection_resource_id = azurerm_storage_account.example.id
    subresource_names              = ["blob"]
    is_manual_connection           = false
  }

  lifecycle {
    ignore_changes = [
      # Ignore changes to private_dns_zone_group, e.g. because an Azure Policy
      # updates it automatically.
      private_dns_zone_group,
    ]
  }
}
```

### AzAPI Terraform provider (using `azapi_update_resource`)

If you are using the [AzAPI Terraform Provider](https://learn.microsoft.com/en-us/azure/developer/terraform/overview), specifically the [azapi_update_resource](https://registry.terraform.io/providers/azure/azapi/latest/docs/resources/update_resource) resource, be aware of the following limitation:

!!! quote "Unchanged properties"
    When you delete an `azapi_update_resource`, **no operation will be performed**, and these properties will stay **unchanged**. If you want to restore the modified properties to some values, you must apply the restored properties **before deleting**.

This means, changes to the `azapi_update_resource` resource may _appear_ to apply changes (ie. remove properties/configurations previous added according to the `terraform plan` output), but this doesn't actually apply those changes in Azure.
