# On-premises connectivity with Azure ExpressRoute

Last updated: **{{ git_revision_date_localized }}**

The following sections explain how to set up hybrid connectivity in Azure with ExpressRoute and connect a Project Set’s virtual network to an on-premises network.

!!! info "Early Adopters"
    Azure ExpressRoute is currently available only for early adopters.  
    Please contact the Public Cloud Team at public.cloud@gov.bc.ca for more information about eligibility and access or or create a ticket via the [Service Desk portal](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

## Overview

Azure ExpressRoute provides a private connection between an organization’s on-premises infrastructure and Azure data centres. Because this connection does not travel over the public internet, it offers stronger security, reliability and performance.

In our setup, ExpressRoute also encrypts data in transit with IPsec. This ensures data stays secure while moving between Azure and on-premises networks.

## Azure to on-premises connectivity

To connect an Azure virtual network to an on-premises network, follow these steps.

![ExpressRoute Connectivity](../images/azure-express_route-connectivity.png "ExpressRoute Connectivity")

1. Traffic leaves  the **Azure Virtual Network (VNet)** and goes to the centralized **Azure Firewall**, which acts as a security boundary and controls the flow of traffic
  - Configure the Azure Firewall with the correct rules to allow traffic from the Azure VNet to the on-premises network. Submit an Azure [Firewall change request](https://citz-do.atlassian.net/servicedesk/customer/portal/3/group/18)
2. After passing through the **Azure Firewall**, the traffic goes to the **ExpressRoute** connection
  - Make sure the Virtual WAN vHub has the correct **private traffic range** configured. Include the private IP address range of the on-premise network you want to connect to
3. **ExpressRoute forwards the traffic** to the on-premises network
  - Configure the on-premises network to **accept traffic from the Azure VNet**
4. On the on-premises side, traffic reaches the **Edge firewall** (called the "**3PG**"). Each on-premises **Zone** also has its own firewall, which must allow traffic from the Azure VNet
  - Submit an on-premises **firewall request** (via iStore) to create a firewall rule that allows traffic from the Azure VNet, through the 3PG firewall and subsequently through the Zone firewall
  - Include the **source** for example your VNet and the **destination** for example your target on-premises network in the request
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

## OpenShift connectivity

If you use OpenShift and want to connect from Azure, submit an [on-premises firewall request form](https://ssbc-client.gov.bc.ca/services/3rdpartygateway/order.htm). For the **Source** or **Destination fields**, enter the appropriate Objects for the OpenShift cluster you’re connecting to.

!!! note "OpenShift firewall objects"
    For specific details on the appropriate OpenShift firewall objects to use, please refer to the [OpenShift documentation](https://digital.gov.bc.ca/technology/cloud/private/internal-resources/topology/).
