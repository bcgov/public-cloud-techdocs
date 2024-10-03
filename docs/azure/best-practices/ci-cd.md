# CI/CD Best Practices

## GitHub Actions

If you are using GitHub Actions for your CI/CD pipeline, consider the following best practices:

* Use [OpenID Connect (OIDC) authentication](deploy-to-the-azure-landing-zone.md#configuring-github-action-oidc-authentication-to-azure) for GitHub Actions to authenticate with Azure.

* [Self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners) on Azure are required to access data storage and database services from GitHub Actions. Public access to these services is not supported.

* If using [Terraform](https://www.terraform.io/), be aware of the limitations when [creating Subnets](../best-practices/be-mindful.md#using-terraform-to-create-subnets), and the use of the [AzAPI Terraform Provider](be-mindful.md#azapi-terraform-provider-using-azapi_update_resource)
