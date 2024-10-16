# DNS for Domain Join

Last updated: **October 3, 2024**

## Overview

To support domain join for Windows-based virtual machines (VMs) in the Azure Landing Zone, a Domain Name System (DNS) service is required. This DNS service is used to resolve the Fully Qualified Domain Name (FQDN) of the domain controller(s) that the VMs will join.

### Current status

Preliminary discussions have been initiated to determine the best approach for implementing the DNS service within the Azure Landing Zone. The team is evaluating the use of Microsoft Entra Domain Services or a custom DNS solution to meet the requirements of domain join.
