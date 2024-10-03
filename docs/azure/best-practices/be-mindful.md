# Be Mindful

The following are some things to be aware of when working within the Azure Landing Zone.

## Virtual Network (VNet) Integration

If you are using an [Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/overview), and you plan to [integrate it with an Azure Virtual Network](https://learn.microsoft.com/en-us/azure/app-service/overview-vnet-integration), it is important to be aware of the following limitation: _You can't delete a subnet that has previously had an integrated App Service, if the integration has not been removed_.

As a best practice for using Azure App Services with VNet integration, if you plan to delete the App Service, ensure that you **remove the integration** with the Virtual Network **before** deleting the App Service. This will allow you to delete the associated Subnet without any issues.

## Private Endpoints and DNS

As a security requirement, some Azure services (ie. Databases, Key Vaults, etc.) have been restricted to private-only connectivity. This means during deployment, you will need to include the creation of a [Private Endpoint](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview) for this service.

As part of creating the Private Endpoint, you will be asked about **Private DNS Integration**. The Azure portal defaults the "**Integrate with private DNS zone**" option to "**Yes**". However, we have the Azure Landing Zones already configured with custom Private DNS Zones, so you should select "**No**" for this option.

![Private Endpoint - Private DNS Integration](../images/private-endpoints-dns.png "Private Endpoint - Private DNS Integration")

Once your resource is deployed, a DNS `A-record` will be automatically created in the custom Private DNS Zone in approximately **10 minutes**, pointing to the private IP address of the resource. This will allow you to access the resource using the custom DNS name within the private network.

However, since the endpoint is private-only, you will not be able to access the resource from outside the VNet. To access and work with these specific resources, you need to use either [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview) or [Azure Virtual Desktop (AVD)](https://learn.microsoft.com/en-us/azure/virtual-desktop/overview) from within the VNet.

In the future, once [Express Route](../upcoming-features/express-route.md) is available, you will also be able to access these resources from the on-premises network.

## Using Terraform to Create Subnets

If you are using Terraform to create your infrastructure, in particular the subnets within your assigned Virtual Network, please be aware of the following challenge.

The Azure Landing Zones have an Azure Policy implemented that requires every subnet to have an associated Network Security Group (NSG) for security controls compliance. The issue is that Terraform does not support the creation of subnets with an associated NSG in a _single step_.

Therefore, instead of using the `azurerm_subnet` resource to create subnets, you must use the `azapi_update_resource` resource from the [AzAPI Terraform Provider](https://registry.terraform.io/providers/Azure/azapi/latest/docs). This resource allows you to create subnets with an associated NSG in a single step.

**Example code:**

```hcl
resource "azapi_update_resource" "subnets" {
  type = "Microsoft.Network/virtualNetworks/subnets@2023-04-01"

  name      = "SubnetName"
  parent_id = data.azurerm_virtual_network.vnet.id
  # Note: Discovered the `locks` attribute for AzAPI from the following GitHub Issue: https://github.com/Azure/terraform-provider-azapi/issues/503
  # A list of ARM resource IDs which are used to avoid create/modify/delete azapi resources at the same time.
  locks = [
    data.azurerm_virtual_network.vnet.id
  ]

  body = jsonencode({
    properties = {
      networkSecurityGroup = {
        id = azurerm_network_security_group.id
      }
    }
  })

  response_export_values = ["*"]
}
```

For further details about this limitation, please refer to the following GitHub Issue: [Example of using the Subnet Association resources with Azure Policy](https://github.com/hashicorp/terraform-provider-azurerm/issues/9022).

## AzAPI Terraform Provider (using `azapi_update_resource`)

If you are using the [AzAPI Terraform Provider](https://learn.microsoft.com/en-us/azure/developer/terraform/overview), specifically the [azapi_update_resource](https://registry.terraform.io/providers/azure/azapi/latest/docs/resources/update_resource) resource, be aware of the following limitation: _When you delete `azapi_update_resource`, no operation will be performed, and these properties will stay unchanged. If you want to restore the modified properties to some values, you must apply the restored properties before deleting_.

This means, changes to the `azapi_update_resource` resource may _appear_ to apply changes (ie. remove properties/configurations previous added according to the `terraform plan` output), but this doesn't actually apply those changes in Azure.
