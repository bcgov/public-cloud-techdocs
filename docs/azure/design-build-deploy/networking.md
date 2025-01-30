# Networking within the Azure Landing Zone

Last updated: **{{ git_revision_date_localized }}**

The following sections describe the networking components within the Azure Landing Zone, including the Virtual Network (VNet), spoke-to-spoke connectivity, and internet connectivity.

## Virtual network (VNet)

Each Project Set in the Azure Landing Zone includes a [Virtual Network (VNet)](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview) which isolates and secures deployed resources. This VNet forms the foundation of network connectivity in the Azure Landing Zone.

!!! danger "VNet CIDR Changes"
    The CIDR range for the VNet is defined at the time of deployment, and cannot be changed after deployment. By default, each Project Set is provided with a `/24` CIDR range. If you require a change to the  CIDR range, please [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team.

This VNet is connected with the central hub (vWAN), and receives default routes to direct all traffic (ie. Internet and private) through the firewall located in the central hub.

There are no subnets that are pre-created within the VNet. Each team is responsible for creating their own subnets based on their requirements. Subnets should be created within the VNet to segment resources based on their function or security requirements.

!!! danger "Security Controls for Subnets"
    There are some security controls in place, that require every subnet to have an associated [Network Security Group (NSG)](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview). This may cause some challenges when creating subnets. The simplest approach is to create a NSG first, and then create the subnet (with the NSG associated with it).

    For further guidance on creating subnets with associated NSGs (specifically using Terraform), refer to the [Be Mindful](../best-practices/be-mindful.md#using-terraform-to-create-subnets) documentation.

    Additionally, as part of implementing a **Zero Trust** security model, all subnets need to be created as [Private Subnets](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/default-outbound-access#utilize-the-private-subnet-parameter-public-preview).

## Spoke-to-Spoke connectivity

If your team has multiple environments (ie. Dev, Test, Prod, Tools) within the same Project Set, you may require connectivity between the different environments. This is known as spoke-to-spoke connectivity.

By default, this connectivity is disabled for security reasons. If you require spoke-to-spoke connectivity, you must [submit a request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team, who will review the request based on the security requirements, and make any necessary changes in the firewall to allow this type of traffic.

## Internet connectivity

All outbound traffic from the Azure Landing Zone is routed through the central hub and the firewall. This ensures that all traffic is inspected and monitored for security compliance.

Advanced features are implemented and configured including:

* Transport Layer Security (TLS) inspection
  * Protection against malicious traffic that is sent from an internal client hosted in Azure to the Internet
  * Protection against East-West traffic that goes to/from an Azure Virtual Network (VNet), to protect Azure workloads from potential malicious traffic sent from within Azure
* Intrusion Detection and Prevention (IDPS)
  * Signature-based detection (applicable for both application and network-level traffic)
* URL filtering
  * Applied both on HTTP and HTTPS traffic
  * Target URL extraction and validation
* Web categories
  * Allow or deny access to websites based on categories (eg. gambling, social media, etc.)

### Exposing services to the internet

For more complex applications, an [Azure Application Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/overview) is the preferred method for exposing your application to the Internet. It provides a web traffic (OSI layer 7) load balancer that enables you to manage traffic to your web applications.

To adhere to security best practices, the Application Gateway should also be configured with a [Web Application Firewall (WAF)](https://learn.microsoft.com/en-us/azure/application-gateway/features#web-application-firewall) to protect your applications from common exploits and vulnerabilities.

## Protected network resources

In order to maintain the security of the Azure Landing Zone, there are certain network resources that are protected and cannot be modified by teams, and other network resources that cannot be created in the Landing Zone. These include:

* Modifying the Virtual Network (VNet) DNS settings
  * This is required so that all traffic is routed through the central firewall, for compliance requirements
* Creating Express Route circuits, VPN Sites, VPN/NAT/Local Gateways, or Route Tables
  * This is so that traffic is not bypassing the central firewall
* Creating Virtual Networks
  * This is to avoid overlapping IP address ranges that may be in use by other Project Sets
* Creating Virtual Network peering with other VNets
  * This is required to ensure spoke-to-spoke traffic is managed centrally through the firewall
* Deleting the `setbypolicy` Diagnostics Settings
  * You can create your own Diagnostics Settings for your resources, but you cannot delete the default one
