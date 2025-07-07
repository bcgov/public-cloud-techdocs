# Networking within the Azure Landing Zone

Last updated: **{{ git_revision_date_localized }}**

The following sections describe the networking components within the Azure Landing Zone, including the Virtual Network (VNet), spoke-to-spoke connectivity, and internet connectivity.

!!! warning "Subnet planning"
    It is **crucial** that you plan out your subnetting strategy **before** deploying resources in the Azure Landing Zone. This will help prevent any potential issues that would require re-architecting your network later on.

## Virtual network (VNet)

Each Project Set in the Azure Landing Zone includes a [Virtual Network (VNet)](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview) which isolates and secures deployed resources. This VNet forms the foundation of network connectivity in the Azure Landing Zone.

!!! danger "Allocated IP addresses"
    Each Project Set is provided with approximately **251 IP addresses** (ie. `/24`) by default. If your application requires more IP addresses than the `/24` provides, contact the Public cloud team by submitting a [Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

    !!! note "Microsoft IP reservations"
        Microsoft **reserves 5 IP addresses** from **each subnet** within a Virtual Network. Therefore a `/24` subnet would not have 256 IP addresses available for use, but rather 251 IP addresses.

This VNet is connected with the central hub (vWAN), and receives default routes to direct all traffic (ie. Internet and private) through the firewall located in the central hub.

There are no subnets that are pre-created within the VNet. Each team is responsible for creating their own subnets based on their requirements. Subnets should be created within the VNet to segment resources based on their function or security requirements.

!!! danger "Security controls for subnets"
    There are some security controls in place, that require every subnet to have an associated [Network Security Group (NSG)](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview). This may cause some challenges when creating subnets. The simplest approach is to create a NSG first, and then create the subnet (with the NSG associated with it).

    For further guidance on creating subnets with associated NSGs (specifically using Terraform), refer to the [IaC and CI/CD](../best-practices/iac-and-ci-cd.md#using-terraform-to-create-subnets) documentation.

    Additionally, as part of implementing a **Zero Trust** security model, all subnets need to be created as [Private Subnets](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/default-outbound-access#utilize-the-private-subnet-parameter-public-preview).

## Spoke-to-Spoke connectivity

If your team has multiple environments (ie. Dev, Test, Prod, Tools) within the same Project Set, you may require connectivity between the different environments. This is known as spoke-to-spoke connectivity.
<!-- TODO: Update to point to the Firewall Request Form once it is released -->
By default, this connectivity is disabled for security reasons. If you require spoke-to-spoke connectivity, you must [submit a request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public cloud team, who will review the request based on the security requirements, and make any necessary changes in the firewall to allow this type of traffic.

## Internet connectivity

All outbound traffic from the Azure Landing Zone is routed through the central hub and the firewall. This ensures that all traffic is inspected and monitored for security compliance.

Advanced features are implemented and configured including:

- Transport Layer Security (TLS) inspection
  - Protection against malicious traffic that is sent from an internal client hosted in Azure to the Internet
  - Protection against East-West traffic that goes to/from an Azure Virtual Network (VNet), to protect Azure workloads from potential malicious traffic sent from within Azure
- Intrusion Detection and Prevention (IDPS)
  - Signature-based detection (applicable for both application and network-level traffic)
- URL filtering
  - Applied both on HTTP and HTTPS traffic
  - Target URL extraction and validation
- Web categories
  - Allow or deny access to websites based on categories (eg. gambling, social media, etc.)

### Exposing services to the internet

For applications with advanced requirements, an [Azure Application Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/overview) is the recommended way to expose applications to the Internet. It includes an OSI Layer 7 web traffic load balancer to help manage web application traffic.

To adhere to security best practices, the Application Gateway should also be configured with a [Web Application Firewall (WAF)](https://learn.microsoft.com/en-us/azure/application-gateway/features#web-application-firewall) to protect your applications from common exploits and vulnerabilities.

!!! warning "Application Gateway backend health probes"
    Please be aware that the backend health may show a status of **Unknown**. For more information and direction on how to resolve this, see the **Insights on Azure Services** - [Application Gateway](../azure-services/application-gateway.md) documentation.

## VNet integration vs private endpoints

When working with Azure PaaS services, there are multiple ways to [integrate Azure services with virtual networks for network isolation](https://learn.microsoft.com/en-us/azure/virtual-network/vnet-integration-for-azure-services).

While [VNet integration](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-for-azure-services), and [Service Endpoints](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoints-overview) are valid options, the recommended approach is to use [Private Endpoints](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview). This is because there is automation in place that will create the DNS record for the private endpoint in the centralized Private DNS Zone. For more information, please refer to the [Private Endpoints and DNS](../best-practices/be-mindful.md#private-endpoints-and-dns) section on the **Best Practices** page.

## Protected network resources

In order to maintain the security of the Azure Landing Zone, there are certain network resources that are protected and cannot be modified by teams, and other network resources that cannot be created in the Landing Zone. These include:

- Modifying the Virtual Network (VNet) DNS settings
  - This is required so that all traffic is routed through the central firewall, for compliance requirements
- Modifying the Virtual Network (VNet) address space
  - This is required so that overlapping IP address ranges are not created in the Azure Landing Zone
- Creating Express Route circuits, VPN Sites, VPN/NAT/Local Gateways, or Route Tables
  - This is so that traffic is not bypassing the central firewall
- Creating Virtual Networks
  - This is to avoid overlapping IP address ranges that may be in use by other Project Sets
- Creating Virtual Network peering with other VNets
  - This is required to ensure spoke-to-spoke traffic is managed centrally through the firewall
- Deleting the `setbypolicy` Diagnostics Settings
  - You can create your own diagnostics settings for your resources, but you can't delete the default one
