# Networking within the Azure Landing Zone

Last updated: **October 2, 2024**

Within each Project Set deployed in the Azure Landing Zone, a [Virtual Network (VNet)](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview) is created to provide network isolation and security for the resources deployed within it. This VNet is the foundation for all network connectivity within the Azure Landing Zone.

This VNet is connected with the central hub (vWAN), and receives default routes to direct all traffic (ie. Internet and private) through the firewall in the central hub.

There are no subnets that are pre-created within the VNet. Each team is responsible for creating their own subnets based on their requirements. Subnets should be created within the VNet to segment resources based on their function or security requirements.

**IMPORTANT:** There are some security controls in place, that require every subnet to have an associated [Network Security Group (NSG)](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview). This may cause some challenges when creating subnets. The simplest approach is to create a NSG first, and then create the subnet (with the NSG associated with it).

## Spoke-to-Spoke Connectivity

If your team has multiple environments (ie. Dev, Test, Prod, Tools) within the same Project Set, you may require connectivity between the different environments. This is known as spoke-to-spoke connectivity.

By default, this connectivity is disabled for security reasons. If you require spoke-to-spoke connectivity, you must submit a request to the Cloud Pathfinder team, who will review the request based on the security requirements, and make any necessary changes in the firewall to allow this type of traffic.
