# Multi-Cloud Connect Service (MCCS)

Last updated: **{{ git_revision_date_localized }}**

The following sections explain how to set up hybrid connectivity in AWS/Azure and connect a Project Set’s virtual network to an on-premises network.

!!! info "Early Adopters"
    AWS DirectConnect and Azure ExpressRoute are currently available only for early adopters.  
    Please contact the Public Cloud Team at public.cloud@gov.bc.ca for more information about eligibility and access or create a ticket via the [Service Desk portal](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

## Overview

AWS DirectConnect and Azure ExpressRoute provides a private connection between an organization’s on-premises infrastructure and AWS/Azure data centres. Because this connection does not travel over the public internet, it offers stronger security, reliability and performance.

In our setup, DirectConnect and ExpressRoute also encrypts data in transit with IPsec. This ensures data stays secure while moving between AWS/Azure and on-premises networks.

## On-premises connectivity workflow

The following describes the general network connectivity from AWS/Azure to on-premises networks.

To connect an AWS virtual private cloud (VPC) or Azure virtual network (VNet) to an on-premises network, follow these steps.

![Hybrid Connectivity](../../images/shared-services/hybrid-connectivity-aws-azure.png "Hybrid Connectivity")

1. Traffic leaves  the cloud **VPC/VNet** and goes to the centralized **cloud firewall**, which acts as a security boundary and controls the flow of traffic
2. After passing through the **cloud firewall**, the traffic goes to the **hybrid** connection (i.e. DirectConnect or ExpressRoute)
3. DirectConnect or ExpressRoute **forwards the traffic** to the on-premises network
4. Traffic reaches the on-premises **edge firewall** (called the "**3PG**"). Each on-premises **Zone** also has its own firewall, which must allow traffic from the cloud VPC/VNet

## Request workflow

To establish connectivity from AWS/Azure to on-premises networks, follow the steps below.

1. Submit a **cloud** [firewall change request](https://citz-do.atlassian.net/servicedesk/customer/portal/3/group/18) to have the cloud firewall configured with the correct rules to allow traffic from the AWS VPC / Azure VNet to the on-premises edge firewall.
2. Submit an **on-premises** [firewall request](https://ssbc-client.gov.bc.ca/services/3rdpartygateway/order.htm) (via iStore) to create a firewall rule that allows traffic from the AWS VPC / Azure VNet, through the 3PG firewall and subsequently through the required Zone firewalls
  - Include the **source** (for example your VPC/VNet) and the **destination** (for example your target on-premises network or endpoint) in the request

!!! warning "On-premises initiated traffic"
    If an on-premises resource needs to **initiate traffic to a cloud resource**, request a separate firewall rule for that traffic flow.

!!! tip "Shared responsibility"
    Traffic from AWS/Azure to on-premises networks is secured and **encrypted** using IPsec over DirectConnect or ExpressRoute.

    Once the traffic reaches your **on-premises network**, you are responsible for keeping the data secure. This includes managing connectivity and data transfers within the on-premises network between resources and zones.

    We set up the **connection** between AWS/Azure and your on-premises network through the cloud firewall. However, **you are responsible** for submitting the required firewall requests for the **on-premises firewalls** to allow traffic from AWS/Azure to reach your target on-premises resources. 
    
    Think of it as us driving you to the door; you still need the right key to get in.

    An example request form for reference is provided below.

## Example request form

This example shows how to request connectivity between a cloud network and an on-premises network.

In the **Object Table** create a **Network IP Range Object** for the AWS VPC or Azure VNet.

![Example Azure VNet](../../images/shared-services/azure-vnet-example.png "Example Azure VNet")

![STMS Firewall Change Request - Add Object](../../images/shared-services/firewall-request-add-object-example.png "STMS Firewall Change Request - Add Object")

In the **Traffic Table** add a **traffic rule** using the naming pattern of `MCCS_(Ministry Short Name)_{LZA/ALZ}_LIVE_(ProjectSet License Plate)_(ENV)` to allow traffic from the AWS VPC or Azure VNet to the on-premises network. Set the `Source` to the AWS VPC or Azure VNet address space and the `Destination` to the on-premises endpoint or network address space.

For example: `MCCS_CITZ_ALZ_LIVE_abc123_prod`.

![STMS Firewall Change Request - Add Traffic](../../images/shared-services/firewall-request-add-traffic-table-example.png "STMS Firewall Change Request - Add Traffic")

!!! warning "Bi-directional traffic"
    The above rule example only allows traffic to flow from the cloud network to on-premises.

    If an on-premises system needs to initiate traffic to a cloud resource, add **another rule** in the Traffic Table for that flow.

## OpenShift connectivity

If you use one of the on-premises OpenShift clusters and want to connect to it from AWS/Azure, submit an [on-premises firewall request form](https://ssbc-client.gov.bc.ca/services/3rdpartygateway/order.htm). For the **Source** or **Destination fields**, enter the appropriate firewall "Objects" for the OpenShift cluster endpoints you’re connecting to.

!!! note "OpenShift firewall objects"
    For specific details on the appropriate OpenShift firewall objects to use, please refer to the [OpenShift documentation](https://digital.gov.bc.ca/technology/cloud/private/internal-resources/topology/).
