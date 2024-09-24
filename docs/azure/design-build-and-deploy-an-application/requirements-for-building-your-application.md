# Requirements for building your application in Azure

Last updated: **September 24, 2024**

The following sections describe the requirements for building your application on the B.C. Government Public Cloud, Azure Landing Zone.

## Prerequisites

1. Create a [provisioning request for a project set](../../get-started/provision-a-project-set.md) for your team on the B.C. Government Public Cloud

2. Once approved, your project set will be provisioned followed up by an email sent to the Product Owner and Technical Lead once the provisioning is complete

3. Learn how to manage user access to Azure by reviewing [User management in Azure](user-management.md)

## Limitations of the Azure Landing Zone

Take the following into consideration when building your application on the Azure Landing zone:

* There is no direct (private) connectivity to the B.C. government network. Any application requiring access to data on this network must use a public endpoint

* Only HTTPS applications are compatible with public endpoints through Azure Application Gateway are supported

* The only supported regions are Canada Central - canadaeast and Canada East - canadacentral

* Most networking is under the management of Azure Landing Zone and is not subject to change.

## Other requirements and best practices

To use GitHub Actions for deploying your application, [OpenID Connect (OIDC) authentication](deploy-an-app-to-the-azure-landing-zone.md#configuring-github-action-oidc-authentication-to-azure) is required.

Self-hosted runners, on Azure, are required to access data storage and database services from GitHub Actions. Public access to these services is not supported.

To deploy your application:

* Use a CI/CD pipeline
* Use infrastructure as code, such as Terraform
* Set up a monitoring solution for your application
* Through the [Product Registry](https://registry.developer.gov.bc.ca/login) configure budgets to receive notifications when your quota is close to being exceeded
* Only grant access to your Azure subscriptions for those who actually need it

## Next steps

* [Deploy an application to the B.C. Government Azure Landing Zone](deploy-an-app-to-the-azure-landing-zone.md)

## Related pages

* [Provision a project set](../../get-started/provision-a-project-set.md)
* [User management in Azure](user-management.md)
* [Configuring GitHub Action OIDC Authentication to Azure](deploy-an-app-to-the-azure-landing-zone.md#configuring-github-action-oidc-authentication-to-azure)
* [Deploy an application to the B.C. Government Azure Landing Zone](deploy-an-app-to-the-azure-landing-zone.md)
