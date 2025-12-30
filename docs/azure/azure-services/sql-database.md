# Azure SQL Database

Last updated: **{{ git_revision_date_localized }}**

Azure SQL Database is a fully managed relational database service (PaaS) that provides high availability, data protection, and scalability.

## Networking and Security

In the Azure Landing Zone, Azure SQL Database is subject to several security guardrails to ensure compliance and protection of data.

### Private Endpoints

Access to Azure SQL Database is restricted to **Private Endpoints only**. Public network access is disabled by default and cannot be enabled. This ensures that your database is only accessible from within your virtual network or peered networks.

For more information on setting up private endpoints, refer to the [Private Endpoints and DNS](../best-practices/be-mindful.md#private-endpoints-and-dns) section.

### Authentication

Azure SQL Database in the Landing Zone is configured to use **Microsoft Entra ID (formerly Azure AD) authentication only**. SQL authentication is disabled to improve security and centralize identity management.

## Portal Connectivity "Gotchas"

!!! warning "SQL Query Editor"
    The **Query Editor** available in the Azure portal attempts to connect to the SQL Database directly from your browser. Because the database is protected by a Private Endpoint, this connection will fail if you are accessing the portal from your local machine outside the VNet.

    To use the Query Editor, you must:
    1. Connect to a **Jump Host** (Virtual Machine) within your VNet using [Azure Bastion](../tools/bastion.md).
    2. Open a browser on that Jump Host and sign in to the Azure portal.
    3. Access the SQL Query Editor from that session.

## Best Practices

* **Enable Auditing:** Ensure auditing is configured to track database activities.
* **TDE:** Transparent Data Encryption (TDE) is enabled by default and must remain enabled.
* **Minimum TLS:** Ensure your applications use TLS 1.2 or higher when connecting to the database.
