# B.C. government OCIO Azure Landing Zone overview

Last updated: **{{ git_revision_date_localized }}**

An overview of the B.C. government OCIO's Landing Zone in Azure, how to get access, its benefits, components, and features.

---

## Benefits of building apps in the Azure public cloud

The OCIO Landing Zone helps B.C. government ministry teams build secure, compliant, and efficient applications in the Azure public cloud. It supports the development of innovative and responsive apps that better serve people in B.C.

The [Microsoft Cloud Adoption Framework (CAF)](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/overview), and [Well-Architected Framework (WAF)](https://learn.microsoft.com/en-us/azure/well-architected/what-is-well-architected-framework) ensures that you can develop and deploy applications in a secure, compliant, and controlled Azure environment, enabling you to focus on delivering innovative and effective digital services.

The following diagram illustrates the Cloud Adoption Framework (CAF) and the various components that support the Landing Zones. For more information, please refer to the official Microsoft documentation on [What is an Azure Landing Zone?](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/)

[![Microsoft Azure Landing Zone Architecture](../images/azure-landing-zone-architecture-diagram-hub-spoke.svg "Microsoft Azure Landing Zone Architecture")](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/media/azure-landing-zone-architecture-diagram-hub-spoke.svg#lightbox)

## Components and features

In this section, we'll provide a high level overview of the components and features of the OCIO's Landing Zone in Azure.

### Security guardrails

The built-in Azure Policy [Regulatory Compliance](https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-initiatives#regulatory-compliance) initiatives provides a security framework for B.C. government ministry teams developing applications in the Azure Public cloud. This framework includes both preventative and detective controls to ensure a secure and compliant cloud environment.

[Microsoft Defender for Cloud (MDfC)](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction) (formerly known as Security Center) offers a centralized dashboard for assessing the security posture of your resources in Azure. Defender for Cloud frameworks such as the [Microsoft Cloud Security Benchmark](https://learn.microsoft.com/en-us/security/benchmark/azure/overview), [CIS Microsoft Azure Foundations Benchmark](https://learn.microsoft.com/en-us/azure/governance/policy/samples/cis-azure-2-0-0), [Cloud Security Posture Management (CSPM)](https://learn.microsoft.com/en-us/azure/defender-for-cloud/concept-cloud-security-posture-management), [NIST SP 800-53](https://learn.microsoft.com/en-us/azure/governance/policy/samples/nist-sp-800-53-r5), and [Canada Federal PBMM](https://learn.microsoft.com/en-us/azure/governance/policy/samples/canada-federal-pbmm) have been enabled for comprehensive checks against Azure resources.

[![Defender for Cloud Overview](../images/defender-for-cloud-overview.png "Defender for Cloud Overview")](https://learn.microsoft.com/en-us/azure/reusable-content/ce-skilling/azure/media/defender-for-cloud/overview.png#lightbox)

For more information see [Azure Guardrails](guardrails.md).

### Networking

The Cloud Adoption Framework (CAF) implements a hub-and-spoke network topology. The hub is the central point of connectivity to the on-premises network, and the spoke is the virtual network that connects to the hub. The hub-and-spoke model allows for the centralization of services and management, while providing isolation and segmentation for workloads.

The B.C. government has implemented the hub-and-spoke module using the modern [Virtual WAN (vWAN)](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about) architecture. Within this architecture, each Project Set is provisioned with a spoke Virtual Network (VNet) that connects to the Virtual Hub (vHub).

[![Virtual WAN Network Topology](../images/virtual-wan-topology.png "Virtual WAN Network Topology")](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/media/virtual-wan-topology.png#lightbox)

For additional information, please refer to the [Networking within the Azure Landing Zone](../design-build-deploy/networking.md) documentation.

### Monitoring and logging

The Cloud Adoption Framework (CAF) implements the components necessary for centralized monitoring and logging, include: [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/overview), [Azure Activity Logs](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/activity-log-insights), [Azure Metrics](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-platform-metrics), and a centralized [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview).

!!! note "Team Responsibility"
    Each team is responsible for configuring the monitoring and logging of their resources, including the collection of metrics, logs, and alerts.

While Microsoft provides various "insights or solutions" for popular services (ie. [Storage Insights](https://learn.microsoft.com/en-us/azure/storage/common/storage-insights-overview), [VM Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-overview), [Container Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview)), these do not cover every Azure service.

As a starting point, each team can assess whether a custom deployment of the [Azure Monitor Baseline Alerts](https://azure.github.io/azure-monitor-baseline-alerts/welcome/) (AMBA) meets their needs. AMBA helps answer the question: **“What should we monitor in Azure?”** It offers baseline alerts based on Microsoft’s recommended practices for proactive monitoring. These include setting up [alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview), [thresholds](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-dynamic-thresholds) and notifications to detect and respond to issues quickly. AMBA also includes a general-purpose [Action Group](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/action-groups) and [Alert Processing R](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-processing-rules?tabs=portal) that can send notifications through email, SMS, and other channels.

[![Azure Monitor Baseline Alerts](../images/azure-monitor-baseline-alerts-policy-initiative-flow.svg "Azure Monitor Baseline Alerts")](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/media/azure-monitor-baseline-alerts-policy-initiative-flow.svg#lightbox)

You can also create custom [Azure Dashboards](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards) to visualize and monitor your resources.

[![Azure Monitor Dashboard](../images/azure-monitor-dashboard-example.png "Azure Monitor Dashboard")](https://learn.microsoft.com/en-us/azure/azure-monitor/media/visualizations/dashboard.png)

For additional information and guidance, please refer to the Microsoft [Advanced Alerting Strategies for Azure Monitoring](https://techcommunity.microsoft.com/blog/startupsatmicrosoftblog/advanced-alerting-strategies-for-azure-monitoring/4268698) article.

## Next steps

- [Deploy to the Azure Landing Zone](../design-build-deploy/deploy-to-the-azure-landing-zone.md)

## Related pages

- [Public cloud services](https://digital.gov.bc.ca/technology/cloud/public)
- [Public cloud hosting 101](https://digital.gov.bc.ca/technology/cloud/public/intro/)
- [Deploy to the Azure Landing Zone](../design-build-deploy/deploy-to-the-azure-landing-zone.md)
- [IAM User Management](../design-build-deploy/user-management.md)
