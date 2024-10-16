# CI/CD Best practices

## GitHub Actions

If you are using GitHub Actions for your CI/CD pipeline, consider the following best practices:

* Use [OpenID Connect (OIDC) authentication](#configuring-github-action-oidc-authentication-to-azure) for GitHub Actions to authenticate with Azure.

* [Self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners) on Azure are required to access data storage and database services from GitHub Actions. Public access to these services is not supported.

* If using [Terraform](https://www.terraform.io/), be aware of the limitations when [creating Subnets](../best-practices/be-mindful.md#using-terraform-to-create-subnets), and the use of the [AzAPI Terraform Provider](be-mindful.md#azapi-terraform-provider-using-azapi_update_resource)

### Configuring GitHub Action OIDC Authentication to Azure

To allow GitHub Actions to securely access Azure subscriptions, use OpenID Connect (OIDC) authentication.

For detailed instructions, see the [GitHub Actions OIDC Authentication Guide](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-azure).

Here's a quick summary on how to set it up:

1. The GitHub Identity Provider has already been configured in the Azure subscriptions in your Project Set
2. In your Azure subscription:
  - Create an Entra ID application and a service principal
  - Add federated credentials for the Entra ID application
  - Create GitHub secrets for storing Azure configuration
3. In your GitHub workflows:
  - Add permissions settings for the token
  - Use the [azure/login](https://github.com/Azure/login) action to exchange the OIDC token (JWT) for a cloud access token

This allows GitHub Actions to authenticate to Azure and access resources.
