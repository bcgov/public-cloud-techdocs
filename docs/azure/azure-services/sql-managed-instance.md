# Azure SQL Managed Instance

Last updated: **{{ git_revision_date_localized }}**

Azure SQL Managed Instance is a fully managed relational database service (PaaS). It supports high availability, data protection, and scaling. It is also highly compatible with SQL Server Enterprise Edition.

---

## Networking and security

In the Azure Landing Zone, SQL Managed Instance needs a dedicated subnet in your virtual network. For subnet sizing, see [Determine required subnet size and range for Azure SQL Managed Instance](https://learn.microsoft.com/en-us/azure/azure-sql/managed-instance/vnet-subnet-determine-size?view=azuresql).

### Subnet delegation

Delegate the SQL Managed Instance subnet to the **Microsoft.Sql/managedInstances** service. Azure then manages network settings and keeps service connectivity working.

### Network security groups

Protect the SQL Managed Instance subnet with a Network Security Group (NSG). The deployment process creates required NSG rules for service traffic. Review the rules and update them for your security needs.

### User defined routes

The SQL Managed Instance subnet also needs a User Defined Route (UDR). The deployment process updates the UDR with required SQL Managed Instance routes.

!!! warning "Create user defined routes"
    Landing Zones restrict User Defined Route (UDR) creation to reduce connectivity issues. Before you deploy SQL Managed Instance, [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team to create the UDR resource.

    After the team creates the UDR, associate it with the SQL Managed Instance subnet.

### Private endpoints

Most Azure PaaS services in Landing Zones [require private endpoints](../best-practices/be-mindful.md#private-endpoints-and-dns). Azure SQL Managed Instance is an exception and **does not** require the use of private endpoints.

### Authentication

Use **Microsoft Entra ID authentication** for Azure SQL Managed Instance to improve security.

## Best practices

* **Authentication method:** Use either the `Microsoft Entra-only` or `Both SQL and Microsoft Entra` option
* **Data endpoint:** Set `Public endpoint (data)` to `Disabled`

## Related pages

* [Azure SQL Database](sql-database.md)
* [Be mindful of service constraints](../best-practices/be-mindful.md)
