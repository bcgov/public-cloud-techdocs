# DNS for Domain Join

Last updated: **{{ git_revision_date_localized }}**

## Overview

To support domain join for Windows-based virtual machines (VMs) in the Azure Landing Zone, a Domain Name System (DNS) service is required. This DNS service is used to resolve the Fully Qualified Domain Name (FQDN) of the domain controller(s) that the VMs will join.

### Current status

After setting up hybrid connectivity with Azure ExpressRoute, the next step is to implement a DNS service to resolve domain names for on-premises resources. This service will also support domain joins for VMs in Azure.

The Access and Directory Management Services (ADMS) team is planning the best way to implement the DNS service. The goal is to ensure VMs can join the domain and communicate with on-premises resources.
