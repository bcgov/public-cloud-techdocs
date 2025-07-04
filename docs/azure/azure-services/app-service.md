# App Service

Last updated: **{{ git_revision_date_localized }}**

If you use an [App Service](https://learn.microsoft.com/en-us/azure/app-service/overview), and you plan to [integrate it with an Azure Virtual Network](https://learn.microsoft.com/en-us/azure/app-service/overview-vnet-integration), keep in mind this limitation: **You can't delete a subnet that has previously had an integrated App Service, if the integration has not been removed**.

To follow best practices when using Azure App Services with VNet integration, if you plan to delete the App Service, ensure that you **remove the integration** with the Virtual Network **before** deleting the App Service. This will allow you to delete the associated Subnet without any issues.

!!! failure "Subnet deletion failure"
If you forget to remove the integration, and have deleted the App Service, you will need to [open a support case](../support/enterprise-support.md#how-to-receive-support) with Microsoft Support to have the integration removed before you can delete the Subnet.
