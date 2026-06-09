# External Microsoft services - Databricks

Last updated: **{{ git_revision_date_localized }}**

This page explains how Azure Databricks and Unity Catalog work in our environment. It also describes the current Unity Catalog status for the B.C. government.

!!! question "What does "external" mean in this context?"
    The term "external" is used to indicate that these service's **control plane** and **management interface** are **outside** the Azure Portal. They are still Azure services, but they have their own management plane.

    Azure may be used to provide compute, storage, capacity, billing, etc. for these services.

---

## Azure Databricks and Unity Catalog

While the [Azure Databricks Workspace](https://learn.microsoft.com/en-us/azure/databricks/introduction/) is an Azure resource managed through the Azure Resource Manager, some of its advanced features, like [Unity Catalog](https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/), operate outside the standard Azure management plane.

Every Azure Databrick Account exists at the **Azure tenant** level, **not** the subscription level. This means a single Databricks account can span across multiple Azure subscriptions.

Unity Catalog provides a centralized data governance layer for Databricks assets but is managed through the **Databricks control plane** via the [Databricks account console](https://accounts.azuredatabricks.net), not through the Azure Portal.

| Feature                           | Owned / Managed by         | Managed in Azure portal? | Managed in Databricks account console? |
| --------------------------------- | ------------------------ | --------------- | -------------------- |
| **Azure Databricks Workspace**    | Azure Resource Manager   | ✅ Yes          | ✅ Yes (shared)      |
| **Unity Catalog**                 | Databricks Control Plane | ❌ No           | ✅ Yes               |
| **Metastore (for Unity Catalog)** | Databricks Account       | ❌ No           | ✅ Yes               |

## How Unity Catalog works

- **Unity Catalog** is a centralized **data governance layer** for Databricks. It enables fine-grained access control across data assets such as tables, views, and files.
- It is **not an Azure-native service**. It runs on the **Databricks control plane** and is managed through [Databricks account console](https://accounts.azuredatabricks.net).
- It can enforce **identity-based policies** by using **Microsoft Entra ID** groups through SCIM federation or manual assignment.
- It governs access to data even when storage lives in **Azure Data Lake Storage Gen2** or other Azure services.

!!! info "Unity Catalog Status"
    **Unity Catalog is currently not enabled or governed** within the B.C. government's Azure environment.

    While Azure Databricks is available for use, there is currently no assigned owner or governance process for Unity Catalog.

    Do not configure or use Unity Catalog at this time.

## Practical considerations

- Unity Catalog must be enabled and managed at the **Databricks account level**. This is separate from Azure Portal and subscription controls.
- Unity Catalog remains disabled because there is currently no designated service owner to manage it.
- When enabled, it works across workspaces that are:
    - In the **same Databricks account**
    - In the **same Azure region**
- It provides central management for catalogs, schemas, tables, permissions, lineage, and audit logs.

### Governance implication

If Unity Catalog is introduced in the future, a central **data governance function** must:

- Define and manage shared catalogs.
- Control cross-workspace access policies.
- Maintain consistent audit and lineage tracking.
- Coordinate identity integration with Microsoft Entra ID.

## References

- [Understanding Key Layers of the Azure Databricks Account](https://www.linkedin.com/pulse/understanding-key-layers-azure-databricks-account-julia-f%C3%B8rde-wjp9f)
- [Databricks data residency](https://learn.microsoft.com/en-us/azure/databricks/resources/databricks-geos)
- [High-level Databricks architecture](https://learn.microsoft.com/en-us/azure/databricks/getting-started/high-level-architecture)
