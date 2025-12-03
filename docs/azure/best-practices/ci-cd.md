# Infrastructure-as-Code (IaC) and CI/CD

Last updated: **{{ git_revision_date_localized }}**

## Overview

!!! question "Planning to use across subscriptions?"
    If you plan to deploy the [GitHub self-hosted runners](#github-self-hosted-runners-on-azure) or the [Azure DevOps Managed DevOps Pools](#managed-devops-pools-on-azure) into a different Azure subscription than where your resources will be deployed (ie. in your **Tools** subscription), you will need to [submit a firewall request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public cloud team. This request should state that you need to allow the self-hosted runners/managed devops pool to access resources in another subscription.

## GitHub Actions

If you are using GitHub Actions for your CI/CD pipeline, consider the following best practices:

- Configure [OpenID Connect (OIDC) authentication](#configuring-github-action-oidc-authentication-to-azure) for GitHub Actions to authenticate with Azure.

- If using [Terraform](https://www.terraform.io/), be aware of the limitations when [creating Subnets](./iac.md#using-terraform-to-create-subnets), and the use of the [AzAPI Terraform Provider](./iac.md#azapi-terraform-provider-using-azapi_update_resource).

- [Self-hosted runners](#github-self-hosted-runners-on-azure) on Azure are required to access data storage and database services from GitHub Actions. Public access to these services is not supported.

- [GitHub-hosted runners](#github-hosted-runners-in-an-azure-vnet) can be used in an Azure VNet, but they require additional configuration to ensure secure access to Azure resources.

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

<!-- ### GitHub hosted runners in an Azure VNet

There currently is no Azure Verified Module (AVM) for GitHub-hosted runners in an Azure VNet, so we wrote our own!

For more information on this feature, refer to the [About Azure private networking for GitHub-hosted runners in your enterprise](https://docs.github.com/en/enterprise-cloud@latest/admin/configuring-settings/configuring-private-networking-for-hosted-compute-products/about-azure-private-networking-for-github-hosted-runners-in-your-enterprise) documentation.

You can find the sample Terraform code for deploying GitHub-hosted runners in the [Azure Landing Zone Samples (azure-lz-samples)](https://github.com/bcgov/azure-lz-samples) repo, under the `/tools/cicd_github_hosted_runners_azure_private_network/` directory.

!!! info "Pre-requisites"
    Please take special note of the pre-requisites listed in the README file. It describes the necessary Resource Provider, and values that the module needs to be deployed. -->

## Azure pipelines

If you are using Azure Pipelines for your CI/CD pipeline, consider the following best practices:

- Configure [Azure DevOps Workload identity federation (OIDC) with Terraform](https://devblogs.microsoft.com/devops/introduction-to-azure-devops-workload-identity-federation-oidc-with-terraform/) for Azure Pipelines to authenticate with Azure.

### Managed DevOps Pools on Azure

Microsoft has created an [Azure Verified Module for Managed DevOps Pools](https://github.com/Azure/terraform-azurerm-avm-res-devopsinfrastructure-pool). The Public cloud team has tested this module, and verified that it works within our Azure environment.

We have created a dedicated/centralized GitHub repository to provide sample Terraform code for creating various application patterns, and various tools. This repository is called [Azure Landing Zone Samples (azure-lz-samples)](https://github.com/bcgov/azure-lz-samples).

You can find the sample Terraform code for deploying DevOps Managed Pools in the `/tools/cicd_managed_devops_pools/` directory.

!!! info "Pre-requisites"
    Please take special note of the pre-requisites listed in the README file in the `/tools/cicd_managed_devops_pools/` directory. It describes the necessary resources, and Resource Providers that are required.
