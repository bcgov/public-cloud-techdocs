# External Microsoft services - Azure DevOps

Last updated: **{{ git_revision_date_localized }}**

This page explains how to use Azure DevOps with Azure subscriptions in the Azure Landing Zone.

!!! question "What does 'external' mean in this context?"
    The term "external" is used to indicate that these service's **control plane** and **management interface** are **outside** the Azure Portal. They are still Azure services, but they have their own management plane.

    Azure may be used to provide compute, storage, capacity, billing, etc. for these services.

---

## Azure DevOps

Azure DevOps Services supports configuring billing to be associated with an Azure subscription. This allows you to use the same Azure subscription for both Azure DevOps and Azure resources.

For more information, please refer to the [Azure DevOps - Manage Billing](https://learn.microsoft.com/en-us/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=azure-devops) documentation.

![Azure DevOps - Set Up Billing](../images/azure-devops-billing.png "Azure DevOps - Set Up Billing")
