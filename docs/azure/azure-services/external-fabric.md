# External Microsoft services - Fabric

Last updated: **{{ git_revision_date_localized }}**

This page explains how Microsoft Fabric connects with Azure services and private networking in the Azure Landing Zone.

!!! question "What does 'external' mean in this context?"
    The term "external" is used to indicate that these service's **control plane** and **management interface** are **outside** the Azure Portal. They are still Azure services, but they have their own management plane.

    Azure may be used to provide compute, storage, capacity, billing, etc. for these services.

---

## Microsoft Fabric

[Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/fundamentals/microsoft-fabric-overview) is a unified analytics platform. It includes Fabric experiences such as [Data Factory](https://learn.microsoft.com/en-us/fabric/data-factory/data-factory-overview), [Data Engineering](https://learn.microsoft.com/en-us/fabric/data-engineering/data-engineering-overview), and [Power BI](https://learn.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview). These are Fabric experiences, not standalone [Azure Data Factory](https://learn.microsoft.com/en-us/azure/data-factory/introduction) or [Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/overview-what-is) services. It runs outside Azure. You can still integrate it with Azure services for analytics and data workflows.

!!! question "Do I really need Microsoft Fabric?"
    To request a Fabric capacity for your project or use case, please use the [Fabric Support Form](https://apps.nrs.gov.bc.ca/int/jira/servicedesk/customer/portal/1/create/1701). In your submission, briefly explain the business problem you are looking to solve with Fabric. The [Fabric Support Form](https://apps.nrs.gov.bc.ca/int/jira/servicedesk/customer/portal/1/create/1701) can also be used for general inquiries, feature/enhancement requests, reporting issues/bugs, or requesting architectural guidance.

If your on-premises data source uses a **publicly reachable endpoint** or a **private endpoint**, [provision a project set](../../welcome/provision-a-project-set.md) in the Azure Landing Zone. You need this project set for your [Fabric capacity quota](https://learn.microsoft.com/en-us/fabric/enterprise/fabric-quotas?tabs=Azure).

To connect Microsoft Fabric to your on-premises data source, deploy a [Data Gateway](https://learn.microsoft.com/en-us/data-integration/gateway/). You can either create a [Virtual Network data gateway](https://learn.microsoft.com/en-us/data-integration/vnet/create-data-gateways) in your Azure Landing Zone, or [install an on-premises data gateway](https://learn.microsoft.com/en-us/data-integration/gateway/service-gateway-install) on a server inside the data center. The gateway provides secure data transfer between Microsoft Fabric and your on-premises data source.

### Private connectivity to/from Microsoft Fabric

There are 2 network connectivity options for Microsoft Fabric:

1. **Outbound networking**, also known as "[Managed private endpoints](https://learn.microsoft.com/en-us/fabric/security/security-managed-private-endpoints-overview)", allows secure and private access to data sources **from** Fabric workloads.
2. **Inbound networking**, also known as "[Workspace-level private links](https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-set-up?tabs=fabric-portal)", allows you to manage network access for specific workspaces individually.

!!! question "Why not Azure Private Link?"
    Microsoft Fabric is a SaaS multi-tenant service that runs outside the subscription network boundary. For private connectivity, it needs a publish-and-approve model, not a direct service endpoint model.

    [Private Endpoint](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview) is mainly for clients in the VNet to reach a service privately. Fabric managed private endpoint scenarios are often in the opposite direction. Fabric workloads need to reach your private, on-premises, or custom service.

    [Private Link Service](https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-set-up?tabs=fabric-portal#step-2-create-the-private-link-service-in-azure) is how you publish your private backend as an approved Private Link target. Fabric then connects privately to that published target through Microsoft backbone with explicit approval.

!!! info "Private Link Service Direct Connect (PLSDC)"
    [Private Link Service Direct Connect (PLSDC)](https://learn.microsoft.com/en-us/azure/private-link/configure-private-link-service-direct-connect) is in public preview. Microsoft does not yet support it in Canadian Azure regions.

    When Microsoft enables PLSDC in Canadian Azure regions, you can use a simpler private connection path for Microsoft Fabric. For more details, read [Connecting Microsoft Fabric to on-premises databases with Private Link](https://blog.cloudtrooper.net/2026/02/25/connecting-microsoft-fabric-to-on-premises-databases-with-private-link/).

## Related pages

- [Provision a project set](../../welcome/provision-a-project-set.md)
