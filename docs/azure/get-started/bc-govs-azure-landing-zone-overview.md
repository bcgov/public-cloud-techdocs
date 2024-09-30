# B.C. Government OCIO Azure Landing Zone Overview

Last updated: **September 24, 2024**

An overview of the B.C. Government OCIO's Landing Zone in Azure, how to get access, its benefits, components, and features.

---

## Benefits of building apps in the Azure Public Cloud

Ministry teams in the B.C. government who want to build applications in the Public Cloud can rely on the OCIO's secure and compliant Landing Zone in Azure. It offers a robust, secure, and efficient framework designed to meet the needs and compliance requirements of the B.C. government. This ensures that applications are developed within a secure and well-governed cloud environment. The OCIO's Azure Landing Zone offers several significant benefits:

1. **Enhanced security compliance**: It aligns with high-standard security frameworks like [NIST 800-53](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final) and the [Canadian Centre for Cybersecurity (CCCS) Medium](https://learn.microsoft.com/en-us/compliance/regulatory/offering-cccs-medium) Cloud Control Profile, which is crucial for government applications handling sensitive, Protected B data. This compliance ensures that your applications meet the necessary security standards, providing peace of mind for both developers and stakeholders.

2. **Streamlined development process**: It helps ministry teams concentrate on developing applications, instead of managing foundational infrastructure, by automating various aspects of the cloud environment setup. This boosts the speed of government application development.

3. **Customizable and scalable architecture**: It gives you the freedom to customize the cloud architecture for your project, supporting a variety of application patterns. Whether you're working on small projects, or large complex applications, it adapts to meet your needs.

4. **Operational consistency and governance**: By using OCIO's Landing Zone, you adopt a uniform approach to cloud infrastructure, ensuring consistency. This uniformity is vital for upholding operational standards and governance.

<!-- What tool? -->
5. **Long-term management and evolution**: This tool not only helps deploy applications initially, but also aids in their ongoing management and evolution. This feature is crucial for government applications, which frequently need updates and adjustments to align with changing policy requirements and citizen needs.

6. **Leveraging cutting-egde capabilities in public cloud**: When you build in the Public Cloud, you can tap into a wide range of services and capabilities, spanning from advanced analytics to Artificial Intelligence (AI) and Machine Learning (ML) tools. This integration has the potential to greatly boost the functionality and reach of government applications.

For B.C. government ministry teams developing applications in the Azure Public Cloud, the OCIO Landing Zone provides a secure, compliant, and efficient pathway. This facilitates the creation of innovative and responsive applications that effectively serve the public.

## How to get access

Explore a [comprehensive guide](https://digital.gov.bc.ca/cloud/services/public/onboard) for onboarding your team to the Public Cloud.

## Components and features

In this section, we'll provide a high level overview of the components and features of the OCIO's Landing Zone in Azure.

### Product Registry

The [Product Registry](#LINK) service is a comprehensive solution designed to streamline the process of requesting and creating Azure Project Sets for B.C. government ministry teams. Each Project Set comprises upto four distinct Azure subscriptions: Development (dev), Testing (test), Production (prod), and Tools. This service plays a crucial role, not just in setting up the necessary Azure infrastructure, but also in managing various aspects of a product's lifecycle in the cloud.

### Key Features of the Product Registry Service

1. **Azure Project Set creation**
   - Helps create a set of four Azure subscriptions (Dev, Test, Prod, Tools) customized for the different stages of the application development lifecycle
   - Guarantees a structured and consistent approach to Azure account management, crucial for large-scale and complex cloud environments

2. **Product ownership and technical leadership**
   - Enables the definition and documentation of Product Owners (POs) and Technical Leads (TLs) for each product
   - Improves accountability and role clarity, ensuring that each product has designated points of contact and leadership

3. **Budget management**
   - Empowers the setting of budgets on each Azure subscription within a Project Set
   - Provides alerts when predefined budget thresholds are reached, aiding in effective financial management and cost control

4. **User access management**
   - Manages user access to the Azure subscriptions, ensuring that only authorized personnel have access to the respective development, testing, production, and tooling environments
   - Improves security and governance by regulating access based on roles and responsibilities

The Product Registry service represents a significant step forward in managing cloud resources effectively, offering a centralized, automated, and controlled approach to handle various aspects of Azure subscription management, from initial setup to continuous budgeting and access control. This service is particularly helpful for teams needing to manage many cloud-based products, providing a streamlined and secure solution for Azure cloud management.

### Project Set

A project set consists of four distinct Azure subscriptions for development (dev), testing (test), production (prod), and tooling (tools) environments. This isolates and protects each stage of the deployment lifecycle.

- The dev account is for developers to experiment and test features
- The test account mirrors production and is used for quality assurance testing
- The prod account is the live environment accessed by end users
- The tools account contains shared resources like CI/CD pipelines, container registries, and automation tools

### Security guardrails

The [Azure Policy Regulatory Compliance](https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-initiatives#regulatory-compliance) initiatives provides a security framework for B.C. government ministry teams developing applications in the Azure Public Cloud. This framework includes both preventative and detective controls to ensure a secure and compliant cloud environment.

#### Preventative controls

- **Azure Policy**
  - Policies act as guardrails to protect the architecture and prevent critical guardrails from being turned off
  - They can deny specific or entire categories of API operations at a Resource Group (RG), Azure subscription, or Management Group (MG) level
  - Policies ensure workloads are deployed only in prescribed regions (ie. Canada Central) and restrict access to specific Azure services as necessary
  - Azure Policy is crucial for keeping an eye on and evaluating Azure resource configurations
  - It makes sure that the resources follow the specified policies and standards
  - Azure Policy works seamlessly with various other Azure services, giving a complete picture of the Azure environment and helping maintain the desired configuration

#### Detective controls

1. **Microsoft Defender for Cloud (MDfC)**
   - Defender for Cloud (formerly known as Security Center) offers a centralized dashboard for assessing the security posture of the Azure footprint
   - It aggregates findings from various Azure security services like Microsoft Sentinel, Azure Policy, and others
   - Defender for Cloud frameworks such as the [Microsoft Cloud Security Benchmark](https://learn.microsoft.com/en-us/security/benchmark/azure/overview), [CIS Microsoft Azure Foundations Benchmark](https://learn.microsoft.com/en-us/azure/governance/policy/samples/cis-azure-2-0-0), [Cloud Security Posture Management (CSPM)](Azure CSPM), [NIST SP 800-53](https://learn.microsoft.com/en-us/azure/governance/policy/samples/nist-sp-800-53-r5), and [Canada Federal PBMM](https://learn.microsoft.com/en-us/azure/governance/policy/samples/canada-federal-pbmm) are recommended to be enabled for comprehensive checks against Azure resources

2. **Microsoft Sentinel**
   - Sentinel is a threat detection service that  monitors for malicious activity and unauthorized behavior
   - It uses machine learning, anomaly detection, and integrated threat intelligence to identify and prioritize potential threats
   - Sentinel is required to be enabled at the organization level, with the security account as the administrative account

3. **Azure Policy (Extended)**
   - In addition to its role in preventative controls, Azure Policy also plays a part in detective controls
   - It supports the detection of non-compliant resources and configurations, contributing to overall security monitoring

The [Microsoft Cloud Adoption Framework (CAF)](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/), and [Well-Architected Framework (WAF)](https://learn.microsoft.com/en-us/azure/well-architected/) ensures that you can develop and deploy applications in a secure, compliant, and controlled Azure environment, enabling you to focus on delivering innovative and effective digital services.

<!-- - TODO: Link to the security guardrails page -->

### Networking

For B.C. government ministry teams developing applications in the OCIO's Landing Zone, understanding the high-level overview of the networking components of the Azure environment is essential. These components are designed to ensure secure, efficient, and compliant networking within Azure environments.

#### High-Level Overview of Networking Components

- **Virtual WAN (VWAN)**

- **Virtual Hub (VHub)**

- **Spoke Virtual Networks (VNets)**


**Exposing services to the Internet**

- **Azure Firewall**


<!-- - TODO: Link to the networking page -->

### Logging

For B.C. government ministry teams working in the OCIO's Landing Zone, it's crucial to understand the high-level overview of the logging components integrated into the environment. These components include [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/overview), [Azure Activity Logs](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/activity-log-insights), and a centralized [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview), each playing a vital role in maintaining a secure and compliant cloud environment.

#### Azure Monitor

- **What is Azure Monitor**: Azure Monitor is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. You can use Azure Monitor to maximize the availability and performance of your applications and services. It helps you understand how your applications are performing and allows you to manually and programmatically respond to system events.

#### Azure Activity Logs

#### Log Analytics Workspace

### IAM Users (service accounts)

#### Summary of Features

## Next steps

- [Deploy an application to the B.C. Government Azure Landing Zone](../design-build-and-deploy-an-application/deploy-an-app-to-the-azure-landing-zone.md)

## Related pages

- [Public cloud services](https://digital.gov.bc.ca/cloud/services/public)
- [Public cloud hosting 101](https://digital.gov.bc.ca/cloud/services/public/intro/)
- [Deploy an application to the B.C. Government Azure Landing Zone](../design-build-and-deploy-an-application/deploy-an-app-to-the-azure-landing-zone.md)
- [IAM User Management Service](../design-build-and-deploy-an-application/iam-user-service.md)
