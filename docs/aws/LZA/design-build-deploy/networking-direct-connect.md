# On-premises connectivity with AWS DirectConnect

Last updated: **{{ git_revision_date_localized }}**

The following sections explain how to set up hybrid connectivity in AWS with DirectConnect and connect a Project Set’s virtual private cloud to an on-premises network.

!!! info "Early Adopters"
    AWS DirectConnect is currently available only for early adopters.  
    Please contact the Public Cloud Team at public.cloud@gov.bc.ca for more information about eligibility and access or or create a ticket via the [Service Desk portal](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

## Overview

AWS DirectConnect provides a private connection between an organization’s on-premises infrastructure and Azure data centres. Because this connection does not travel over the public internet, it offers stronger security, reliability and performance.

In our setup, DirectConnect also encrypts data in transit with IPsec. This ensures data stays secure while moving between Azure and on-premises networks.

## AWS to on-premises connectivity

To connect an AWS virtual private cloud network to an on-premises network, refer to the [Shared Services - Multi-Cloud Connect Service (MCCS)](../../../shared-services/mccs/mccs.md) documentation.
