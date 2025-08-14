# On-premises connectivity with Azure ExpressRoute

Last updated: **{{ git_revision_date_localized }}**

The following sections explain how to set up hybrid connectivity in Azure with ExpressRoute and connect a Project Set’s virtual network to an on-premises network.

## Overview

Azure ExpressRoute is a service that provides a private connection between an organization's on-premises infrastructure and Azure data centers. This connection does not go over the public internet, which enhances security, reliability, and performance.

Our configuration of ExpressRoute also include encryption of data in transit (via IPSEC), ensuring that data remains secure while being transmitted between Azure and on-premises networks.

## Azure to on-premises connectivity

To establish connectivity between an Azure virtual network and an on-premises network, the following steps are typically involved.

![ExpressRoute Connectivity](../images/azure-express_route-connectivity.png "ExpressRoute Connectivity")

1. Traffic is routed from the **Azure Virtual Network (VNet)**. This traffic is directed to the centralized **Azure Firewall**, which acts as a security boundary and controls the flow of traffic.
  - To allow traffic to flow from the Azure VNet to the on-premises network, the Azure Firewall must be configured with appropriate rules. Please submit an Azure [Firewall Change Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3/group/18).
2. After passing through the **Azure Firewall**, the traffic is routed to the **ExpressRoute** connection.
  - This requires that the Virtual WAN vHub has the proper **private traffic range** configured. Please ensure that you include the private IP address range of the on-premises network you want to connect to.
3. ExpressRoute then **forwards the traffic** to the on-premises network.
  - The on-premises network must be **configured to accept traffic from the Azure VNet**.
4. On the on-premises side, the traffic is received by the **Edge firewall** (referred to as the "**3PG**"). There are additional firewalls for each respective on-premises **Zone**, which also need to be configured to allow traffic from the Azure VNet.
  - You will need to submit an on-premises **firewall request** (via iStore), requesting a new firewall rule be created to allow traffic from the Azure VNet, through the 3PG firewall, and subsequently through the Zone firewall.
  - You will need to include the **source** (ie. your VNet), and the **destination** (ie. your target on-premises network) in the request.
  - On-premises firewall request form: [Office of the Chief Information Officer: Third Party Gateway Service](https://ssbc-client.gov.bc.ca/services/3rdpartygateway/order.htm)

!!! warning "On-premises initiated traffic"
    If an on-premises resource needs to **initiate traffic to an Azure resource**, request a separate firewall rule for that traffic flow.

## Example request form

This example shows how to request connectivity between an Azure VNet and an on-premises network.

In the **Object Table** create a **Network IP Range Object** for the Azure VNet.

![Example Azure VNet](../images/azure-vnet-example.png "Example Azure VNet")

![STMS Firewall Change Request - Add Object](../images/firewall-request-add-object-example.png "STMS Firewall Change Request - Add Object")

In the **Traffic Table** add a **traffic rule** using the naming pattern of `MCCS_(Ministry Short Name)_ALZ_LIVE_(ProjectSet License Plate)_(ENV)` to allow traffic from the Azure VNet to the on-premises network. Set the `Source` to the Azure VNet address space and the `Destination` to the on-premises network address space.

For example: `MCCS_CITZ_ALZ_LIVE_abc123_prod`.

![STMS Firewall Change Request - Add Traffic](../images/firewall-request-add-traffic-table-example.png "STMS Firewall Change Request - Add Traffic")

!!! warning "Bi-directional traffic"
    If you need bi-directional traffic, for example on-premises to Azure, add another rule in the Traffic Table for that flow.
