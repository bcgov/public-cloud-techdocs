# Azure services

Last updated: **{{ git_revision_date_localized }}**

The following are some things to be aware of when working with specific Azure services within the Azure Landing Zone. This does not list every Azure service, but rather, shares some best practices and limitations for specific services, based on the experience of the Public Cloud team and feedback from end-users.

!!! question "Azure AI and ML services"
    Looking for information on **Artificial Intelligence (AI)** or **Machine Learning (ML)** services? We have a dedicated page for [Azure AI Services](./azure-ai.md).

## Azure services

### Azure App Service

If you use an [Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/overview), and you plan to [integrate it with an Azure Virtual Network](https://learn.microsoft.com/en-us/azure/app-service/overview-vnet-integration), keep in mind this limitation: **You can't delete a subnet that has previously had an integrated App Service, if the integration has not been removed**.

To follow best practices when using Azure App Services with VNet integration, if you plan to delete the App Service, ensure that you **remove the integration** with the Virtual Network **before** deleting the App Service. This will allow you to delete the associated Subnet without any issues.

### API Management service

The current version (v1) of the Azure [API Management Service](https://learn.microsoft.com/en-us/azure/api-management/api-management-key-concepts) doesn't work with [inbound Private Endpoints](https://learn.microsoft.com/en-us/azure/api-management/virtual-network-concepts#inbound-private-endpoint). To access the service from within a virtual network, you must create a DNS record. Unlike other [private endpoints](#private-endpoints-and-dns) in the Landing Zones, this process isn't automated.

If you are using v1 of the API Management Service, please [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team to have the DNS record created for you.

Version 2 of the API Management Service [isn't currently available](https://learn.microsoft.com/en-us/azure/api-management/api-management-region-availability#supported-regions-for-v2-tiers-and-workspace-gateways) in the Canada Azure regions. When available, use this version, as it'll support Private Endpoints.

### Azure Database for PostgreSQL Flexible server

When deploying an [Azure Database for PostgreSQL Flexible server](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/overview), you have multiple network connectivity options, including [Public access (allowed IP addresses) and Private endpoint](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/how-to-networking), and [Private access (VNet Integration)](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/concepts-networking-private#private-access-vnet-integration).

![Azure Database for PostgreSQL Flexible Server - Networking Connectivity](../images/azure-db-postgres-flexible-server-network-connectivity.png "Azure Database for PostgreSQL Flexible Server - Networking Connectivity")

Although the connectivity method says "**public access**", this is the option you want to select, in order to be able to [add private endpoint connections](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/how-to-networking-servers-deployed-public-access-add-private-endpoint?tabs=portal-add-private-endpoint-connections).
