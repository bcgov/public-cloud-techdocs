# DNS for Domain Join

Last updated: **{{ git_revision_date_localized }}**

## Overview

To support domain join for Windows-based virtual machines (VMs) in the Azure Landing Zone, a Domain Name System (DNS) service is required. This DNS service is used to resolve the Fully Qualified Domain Name (FQDN) of the domain controller(s) that the VMs will join.

### Current status

With the hybrid connectivity established via Azure Express Route, the next step is to implement a DNS service that can resolve domain names for on-premises resources. This includes supporting domain join for VMs in the Azure environment.

Planning is underway with the Access and Directory Management Services (ADMS) team to determine the best approach for implementing the DNS service. The goal is to ensure that VMs can successfully join the domain and communicate with on-premises resources.
