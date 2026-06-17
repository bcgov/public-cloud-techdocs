# Continuous Integration and Continuous Deployment (CI/CD) best practices

Last updated: **{{ git_revision_date_localized }}**

## Overview

!!! question "Planning to use across subscriptions?"
    If you plan to deploy the [GitHub self-hosted runners](#github-self-hosted-runners-on-azure) or the [Azure DevOps Managed DevOps Pools](#managed-devops-pools-on-azure) into a different Azure subscription than where your resources will be deployed (ie. in your **Tools** subscription), you will need to [submit a firewall request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public cloud team. This request should state that you need to allow the self-hosted runners/managed devops pool to access resources in another subscription.

## GitHub Actions

If you are using GitHub Actions for your CI/CD pipeline, consider the following best practices:

- Configure [OpenID Connect (OIDC) authentication](#configuring-github-action-oidc-authentication-to-azure) for GitHub Actions to authenticate with Azure.

- If using [Terraform](https://www.terraform.io/), be aware of the limitations when [creating Subnets](./iac.md#using-terraform-to-create-subnets), and the use of the [AzAPI Terraform Provider](./iac.md#azapi-terraform-provider-using-azapi_update_resource).

- [Self-hosted runners](#github-self-hosted-runners-on-azure) on Azure are required to access data storage and database services from GitHub Actions. Public access to these services is not supported.

<!-- TO DO: Add this section back in when we have tested and verified the GitHub-hosted runners in an Azure VNet.
- [GitHub-hosted runners](#github-hosted-runners-in-an-azure-vnet) can be used in an Azure VNet, but they require additional configuration to ensure secure access to Azure resources. 
-->

### Configuring GitHub Action OIDC authentication to Azure

To allow GitHub Actions to securely access Azure subscriptions, use OpenID Connect (OIDC) authentication.

For detailed instructions, see the [GitHub Actions OIDC Authentication Guide](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure).

Here's a quick summary on how to set it up:

1. The GitHub Identity Provider has already been configured in the Azure subscriptions in your Project Set
2. In your Azure subscription:

  - Create an Entra ID Application and a Service Principal
  - Add federated credentials for the Entra ID Application
  - Create GitHub secrets for storing Azure configuration

3. In your GitHub workflows:

  - Add permissions settings for the token
  - Use the [azure/login](https://github.com/Azure/login) action to exchange the OIDC token (JWT) for a cloud access token

This allows GitHub Actions to authenticate to Azure and access resources.

### GitHub self-hosted runners on Azure

Microsoft has created an [Azure Verified Module (AVM) for CICD Agents and Runners](https://github.com/Azure/terraform-azurerm-avm-ptn-cicd-agents-and-runners). The Public cloud team has tested this module, and verified that it works within our Azure environment (with a few customizations).

We have created a dedicated/centralized GitHub repository to provide sample Terraform code for creating various application patterns, and various tools. This repository is called [Azure Landing Zone Samples (azure-lz-samples)](https://github.com/bcgov/azure-lz-samples).

You can find the sample Terraform code for deploying self-hosted GitHub runners in the `/tools/cicd_self_hosted_agents/` directory.

!!! info "Pre-requisites"
    Please take special note of the pre-requisites listed in the README file in the `/tools/cicd_self_hosted_agents/` directory. It describes the necessary subnets that the self-hosted runners need to be deployed in.

### GitHub managed runners in an Azure VNet

GitHub supports Azure private networking for GitHub-hosted runners. This allows GitHub-hosted runners to be deployed in an Azure VNet, and securely access Azure resources that are not publicly accessible (ie. resources that are deployed with Private Endpoints, or resources that are deployed in a private subnet).

We have created a centralized implementation of this feature, and have made it available to all projects in the B.C. government Azure environment.

#### How does this feature work?

The GitHub managed runners are deployed in a dedicated virtual network in a centralized Azure subscription. By default, network connectivity to the Landing Zones is **blocked** through the firewall.

!!! question "How to request access to this feature?"
    If you are interested in using this feature, please submit a **firewall request** to the Public Cloud team through [Jira Service Management (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3). Please include the GitHub **repositories** that you want to be able to use the centralized runners from.

Once the appropriate firewall rules have been configured, and your repository has been enabled, you will be able to use the GitHub managed runners in your CI/CD pipelines by including the following configuration in your GitHub workflow YAML file:

```yaml
jobs:
  job-name:
    name: Job Name
    runs-on:
      group: live-connectivity-runners # GitHub Managed Runners injected into an Azure VNet
    steps:
    - name: Checkout
      uses: actions/checkout@v4
```

!!! danger "Post-enablement"
    After the firewall rules have been configured, you will be responsible for allowing (and blocking) access to your resources from the GitHub managed runners. You can do this through the use of [Network Security Groups (NSGs)](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview).

    One suggestion is to create a pre-step in your GitHub workflow to retrieve the private IP address of the GitHub managed runner, and then dynamically/on-demand update the NSG rules to allow access to your resources. After your workflow has completed, you can then remove the NSG rules to block access again.

## Azure pipelines

If you are using Azure Pipelines for your CI/CD pipeline, consider the following best practices:

- Configure [Azure DevOps Workload identity federation (OIDC) with Terraform](https://devblogs.microsoft.com/devops/introduction-to-azure-devops-workload-identity-federation-oidc-with-terraform/) for Azure Pipelines to authenticate with Azure.

### Managed DevOps Pools on Azure

Microsoft has created an [Azure Verified Module for Managed DevOps Pools](https://github.com/Azure/terraform-azurerm-avm-res-devopsinfrastructure-pool). The Public cloud team has tested this module, and verified that it works within our Azure environment.

We have created a dedicated/centralized GitHub repository to provide sample Terraform code for creating various application patterns, and various tools. This repository is called [Azure Landing Zone Samples (azure-lz-samples)](https://github.com/bcgov/azure-lz-samples).

You can find the sample Terraform code for deploying DevOps Managed Pools in the `/tools/cicd_managed_devops_pools/` directory.

!!! info "Pre-requisites"
    Please take special note of the pre-requisites listed in the README file in the `/tools/cicd_managed_devops_pools/` directory. It describes the necessary resources, and Resource Providers that are required.
