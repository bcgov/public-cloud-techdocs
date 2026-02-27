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

To connect an Azure virtual network to an on-premises network, refer to the [Shared Services - Multi-Cloud Connect Service (MCCS)](../../shared-services/mccs/mccs.md) documentation.

## On-premises to Azure connectivity

If you connect from an on-premises resource to Azure, you may need a local `hosts` file entry.
This also applies when you connect through the B.C. government's VPN.

For example, connect to the VPN and open SQL Server Management Studio.
To reach an Azure SQL Database, add a `hosts` file entry.
Map the database fully qualified domain name (FQDN) to its private IP address.

[Azure DNS](../upcoming-features/azure-dns.md) for this scenario is in development.
Use this workaround for on-premises to Azure connectivity today.
