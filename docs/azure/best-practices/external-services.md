# External Microsoft services

Last updated: **{{ git_revision_date_localized }}**

## Overview

There are several Microsoft services that are commonly used in conjunction with Azure services. These may include services like Azure DevOps, Dynamics 365, Power Platform, etc. This document provides some considerations when working with these services within the Azure Landing Zone.

## Azure DevOps

Azure DevOps Services supports configuring billing to be associated with an Azure subscription. This allows you to use the same Azure subscription for both Azure DevOps and Azure resources.

For more information, please refer to the [Azure DevOps - Manage Billing](https://learn.microsoft.com/en-us/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=azure-devops) documentation.

![Azure DevOps - Set Up Billing](../images/azure-devops-billing.png "Azure DevOps - Set Up Billing")

## Power Platform

Microsoft Power Platform includes Power Apps, Power Automate, Power BI, and Power Virtual Agents. These services can be used to build custom applications, automate workflows, and analyze data.

Power Platform supports configuring billing to be associated with an Azure subscription. This allows you to use the same Azure subscription for both Power Platform and Azure resources.

For more information, please refer to the [Power Platform - Set up pay-as-you-go](https://learn.microsoft.com/en-us/power-platform/admin/pay-as-you-go-set-up?tabs=new) documentation.

!!! info "Information"
    If you plan on using an Azure subscription for Power Platform billing, please **pre-create** the Resource Group where the Power Platform resources will be deployed, and then contact the [Public Cloud team](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to apply the necessary resource region exemptions.

    This is required, because the Azure Landing Zones have a region restriction to `Canada Central` and `Canada East`, but the Power Platform's region selection do not align with the Azure regions. In the Power Platform, you can only select `Canada` as a region, which includes all Canadian regions.

    ![Power Platform - Region Selection](../images/power-platform-region-selection.png "Power Platform - Region Selection")
