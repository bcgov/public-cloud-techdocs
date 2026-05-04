# External Microsoft services - Power Platform

Last updated: **{{ git_revision_date_localized }}**

## Overview

There are several Microsoft services that are commonly used in conjunction with Azure services. These may include services like Azure DevOps, Dynamics 365, Power Platform, etc. This document provides some considerations when working with these services within the Azure Landing Zone.

## Power Platform

Microsoft Power Platform includes [Power Apps](https://learn.microsoft.com/en-us/power-apps/powerapps-overview), [Power Automate](https://learn.microsoft.com/en-us/power-automate/flow-types), [Power BI](https://learn.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview), and Power Virtual Agents (now part of [Microsoft Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-what-is-copilot-studio)). These services can be used to build custom applications, automate workflows, and analyze data.

Power Platform supports configuring billing to be associated with an Azure subscription. This allows you to use the same Azure subscription for both Power Platform and Azure resources.

For more information, please refer to the [Power Platform - Set up pay-as-you-go](https://learn.microsoft.com/en-us/power-platform/admin/pay-as-you-go-set-up?tabs=new) documentation.
