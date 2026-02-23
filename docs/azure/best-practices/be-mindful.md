# Be mindful

Last updated: **{{ git_revision_date_localized }}**

The following are some things to be aware of when working within the Azure Landing Zone.

## Private Endpoints and DNS

As a security requirement, some Azure PaaS services like Databases and Key Vaults restrict access to private-only connectivity. During deployment, you must create a [Private Endpoint](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview) for the service.

As part of creating the Private Endpoint, you'll see a prompt about **Private DNS Integration**. The Azure portal defaults the "**Integrate with private DNS zone**" option to "**Yes**". However, we have the Azure Landing Zones already configured with centralized custom Private DNS Zones, so you should select "**No**" for this option.

![Private Endpoint - Private DNS Integration](../images/private-endpoints-dns.png "Private Endpoint - Private DNS Integration")

After you deploy the Private Endpoint, the system automatically creates a DNS `A-record` in the custom Private DNS Zone within about 10 minutes. This record points to the resource's private IP address. You can then access the resource using the custom DNS name within the private network.

Since the endpoint is private-only, you can only access the resource from within the VNet. To access and work with these specific resources, you need to use either [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview) or an [Azure Virtual Desktop (AVD)](https://learn.microsoft.com/en-us/azure/virtual-desktop/overview) from within the VNet.

!!! warning "Portal-based management tools"
    Many Azure portal-based management tools, such as the **SQL Query Editor** or **Azure Data Factory Studio**, communicate directly with the resource's endpoint from your browser.
    
    If you configure a private endpoint for these services, you can only connect from within the private network. Access the Azure portal from a machine inside the private network, such as a Jump Host accessed via Bastion or AVD.

In the future, once [ExpressRoute](../design-build-deploy/networking-express-route.md) is available, you will also be able to access these resources from the on-premises network or through a VPN.

## Custom DNS zones

In some scenarios, you may need to create a custom DNS Zone. Generally, this is not recommended. The Azure Landing Zones come with centralized custom Private DNS Zones for Azure services. However, for third-party services like Confluent Cloud, a Private DNS Zone might not exist.

If this is your scenario, please submit a [support request](https://citz-do.atlassian.net/servicedesk/customer/portal/3), so that the Public cloud team can work with you to create and attach the custom DNS Zone to the central Private DNS Resolver.

!!! failure "Private DNS Zone attachment to VNet"
    Attaching your custom Private DNS Zone to your Virtual Network (VNet) will not work, as all DNS queries are routed through the central Private DNS Resolver.
