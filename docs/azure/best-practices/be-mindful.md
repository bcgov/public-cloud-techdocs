# Be mindful

Last updated: **{{ git_revision_date_localized }}**

The following are some things to be aware of when working within the Azure Landing Zone.

## Private Endpoints and DNS

As a security requirement, some Azure PaaS services (ie. Databases, Key Vaults, etc.) have been restricted to private-only connectivity. This means during deployment, you will need to include the creation of a [Private Endpoint](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview) for this service.

As part of creating the Private Endpoint, you will be asked about **Private DNS Integration**. The Azure portal defaults the "**Integrate with private DNS zone**" option to "**Yes**". However, we have the Azure Landing Zones already configured with centralized custom Private DNS Zones, so you should select "**No**" for this option.

![Private Endpoint - Private DNS Integration](../images/private-endpoints-dns.png "Private Endpoint - Private DNS Integration")

Once the Private Endpoint for your resource is deployed, a DNS `A-record` will be automatically created in the custom Private DNS Zone in approximately **10 minutes**, pointing to the private IP address of the resource. This will allow you to access the resource using the custom DNS name within the private network.

However, since the endpoint is private-only, you will not be able to access the resource from outside the VNet. To access and work with these specific resources, you need to use either [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview) or an [Azure Virtual Desktop (AVD)](https://learn.microsoft.com/en-us/azure/virtual-desktop/overview) from within the VNet.

In the future, once [Express Route](../upcoming-features/express-route.md) is available, you will also be able to access these resources from the on-premises network or through a VPN.

## Custom DNS Zones

In some scenarios, you may have a need to create a custom DNS Zone. Generally, this is not recommended, as the Azure Landing Zones are already configured with centralized custom Private DNS Zones for the Azure services. However, when working with third-party services (ie. Confluent Cloud), we might not have a Private DNS Zone for the specific service.

If this is your scenario, please submit a [support request](https://citz-do.atlassian.net/servicedesk/customer/portal/3), so that the Public cloud team can work with you to create and attach the custom DNS Zone to the central Private DNS Resolver.

!!! failure "Private DNS Zone attachment to VNet"
    Attaching your custom Private DNS Zone to your Virtual Network (VNet) will not work, as all DNS queries are routed through the central Private DNS Resolver.
