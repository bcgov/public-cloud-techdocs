# Azure Databricks and Unity Catalog

Last updated: **{{ git_revision_date_localized }}**

[Azure Databricks](https://learn.microsoft.com/en-us/azure/databricks/introduction/) is **available for use** in our environment, while [Unity Catalog](https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/) (a governance layer) is currently not enabled. This document outlines the distinction between these components, clarifies where each resides and is managed, and explains the current status of Unity Catalog.

## Key distinctions

| Feature                        | Owned/Managed by         | Lives in Azure? | Lives in Databricks?   |
| ------------------------------ | ------------------------ | --------------- | ---------------------- |
| **Azure Databricks Workspace** | Azure Resource Manager   | ✅ Yes          | ✅ Yes (shared)        |
| **Unity Catalog**              | Databricks Control Plane | ❌ No           | ✅ Yes                 |
| **Metastore**                  | Databricks Account       | ❌ No           | ✅ Yes                 |
| **Azure AD (Entra ID)**        | Microsoft                | ✅ Yes          | Used via federation    |
| **Data Storage (e.g., ADLS)**  | Azure                    | ✅ Yes          | Accessed by Databricks |

## How Unity Catalog works

- **Unity Catalog** is a centralized **data governance layer** for Databricks that enables fine-grained access control across all data assets (tables, views, files).
- It is **not an Azure-native service**. It runs on the **Databricks control plane**, and is provisioned and managed through [https://accounts.azuredatabricks.net](https://accounts.azuredatabricks.net).
- It can enforce **identity-based policies**, using **Microsoft Entra ID** groups via SCIM federation or manual assignment.
- It governs access to data even when the actual storage resides in **Azure Data Lake Storage Gen2** or other Azure-native services.

!!! info "Azure Databricks is available, but Unity Catalog is currently unavailable for use"
    While technically supported within Azure Databricks, **we do not currently have an assigned owner or governance process** for Unity Catalog in our environment.

    As such, Unity Catalog **has not been enabled** in any workspace, and users should **not attempt to configure or use it** at this time.

    Workspaces will continue to rely on **legacy workspace-level access controls** and standard Databricks role-based permissions until further notice.

## Practical considerations

- Unity Catalog must be enabled and managed at the **Databricks Account level** — this is separate from Azure Portal or subscription-level controls.
- Currently, there is no designated service owner to manage Unity Catalog, which is why it remains disabled in our environment.
- When enabled, it only works across workspaces that are:
  - In the **same Databricks Account**
  - In the **same Azure region**
- It offers central management of catalogs, schemas, tables, permissions, lineage, and audit logs.

### Governance implication

If and when Unity Catalog is introduced, a central **data governance function** will be required to:

- Define and manage shared catalogs
- Control access policies at a cross-workspace level
- Maintain consistent audit and data lineage tracking
- Coordinate identity integration with Microsoft Entra ID
