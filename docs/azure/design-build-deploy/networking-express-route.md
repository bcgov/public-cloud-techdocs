# On-premises connectivity with Azure ExpressRoute

Last updated: **{{ git_revision_date_localized }}**

The following sections show you how to set up hybrid connectivity in Azure using ExpressRoute and connect your Project Set’s virtual network to your on-premises network.

!!! info "Early Adopters"
    Azure ExpressRoute is currently available only for early adopters.  
    Please contact the Public Cloud Team at [public.cloud@gov.bc.ca](mailto:public.cloud@gov.bc.ca) for more information about eligibility and access or create a ticket via the [Service Desk portal](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

## Overview

Azure ExpressRoute provides a private connection between an organization’s on-premises infrastructure and Azure data centres. Because this connection does not travel over the public internet, it offers stronger security, reliability and performance.

In our setup, ExpressRoute also encrypts data in transit with IPsec. This ensures data stays secure while moving between Azure and on-premises networks.

## Azure to on-premises connectivity

To establish connectivity between an Azure virtual network and an on-premises network, refer to the [Shared Services - Multi-Cloud Connectivity Service (MCCS)](../../shared-services/mccs/mccs.md) documentation.

## Domain Name System (DNS) resolution

DNS resolves host names to IP addresses. This lets resources in Azure and on-premises networks communicate.

### Azure resolution of on-premises DNS names

Azure workloads can resolve on-premises DNS names, specifically for resources in the `.bcgov`, `.dmz`, and `.gov.bc.ca` domains. If you have an on-premises resource that you need to connect to from Azure outside of these domains, [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team.

### On-premises resolution of Azure DNS names

If an on-premises resource needs to connect to an Azure resource, DNS resolution for resources using Private Link endpoints is now supported through ExpressRoute.

!!! info "DNS resolution from on-premises zones"
    Resolution of Azure resources from on-premises is supported from the `Internal` and `DMZ` zones only. If you are in any other zone, please [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team.

On-premises systems will need to be configured to use the **Private Link FQDN** of the Azure resource. For example, if you have an Azure SQL Database with a Private Link endpoint, the FQDN will be in the format: `<resource-name>.privatelink.database.windows.net`. The public FQDN of the resource (i.e. `<resource-name>.database.windows.net`) will not resolve to the private IP address from on-premises systems.
