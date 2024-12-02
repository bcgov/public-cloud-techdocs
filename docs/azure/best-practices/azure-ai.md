# Azure AI Services

Last updated: **December 2, 2024**

Many of the ministry teams are using Azure AI services to build intelligent applications. Artificial Intelligence and Machine Learning are rapidly changing technologies. The following are some recommendations and guidance based on observations and experiences from the ministry teams.

## Region availability

Although the [Azure AI Foundry (formerly Azure AI Studio)](https://learn.microsoft.com/en-us/azure/ai-studio/what-is-ai-studio) is available in the Canada Azure regions, not all [models](https://azure.microsoft.com/en-us/products/ai-model-catalog?msockid=2274ddfe4fb768de0595c8be4e1d6918#tabs-pill-bar-oc92d8_tab0) or services may be available in the Canada regions. It is recommended to check the availability of the services in the Canada region before starting the development.

The most common Azure AI Services that are used by the ministry teams are:

- Azure OpenAI
- AI Search
- Document Intelligence

## Deploying models

When working with Azure AI services, due to security guardrails that have been put in place (to protect government data from the Internet), you may need to deploy a Virtual Machine within the Azure network to be able to successfully deploy models.

The simplest method to do this, is to deploy an [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/quickstart-host-portal) within your virtual network.

> Note: The minimum Bastion SKU required is **Basic**, as the **Developer** tier is not currently available in the Canada regions.

This does require a specific Subnet to be created within the VNet. The subnet name must be **AzureBastionSubnet**. The subnet address range that you specify must be **/26 or larger** (for example, /25 or /24). After adding this subnet to your virtual network, you can deploy Bastion.

Additionally, you will need to create the appropriate ingress and egress Network Security Group (NSG) rules to allow traffic to and from the Azure Bastion service. Please refer to the [Working with NSG access and Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-nsg#apply) documentation for specific details.

> Note: The rule priority does not need to match the example below, but the rule configuration should be similar.

[![Azure Bastion - Ingress Rules](../images/azure-bastion-inbound-nsg-rules.png "Azure Bastion - Ingress Rules")](https://learn.microsoft.com/en-us/azure/bastion/media/bastion-nsg/inbound.png#lightbox)

[![Azure Bastion - Egress Rules](../images/azure-bastion-outbound-nsg-rules.png "Azure Bastion - Egress Rules")](https://learn.microsoft.com/en-us/azure/bastion/media/bastion-nsg/outbound.png#lightbox)

## Azure OpenAI and Private DNS

When working with Azure OpenAI, you may need to create a Private Endpoint to resolve the Azure OpenAI service endpoints.

It has been observed in several cases, where the DNS `A-Record` for the Azure OpenAI service is not being created properly in the Private DNS Zone. This can cause issues with the service not being able to resolve the endpoint.

If you encounter this issue, please open a [support ticket](../../welcome/support.md) with the Public Cloud Platform support team to investigate and resolve the issue.
