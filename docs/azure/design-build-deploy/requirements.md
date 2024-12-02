# Requirements for building your application in Azure

Last updated: **December 4, 2024**

The following sections describe the requirements for building your application on the B.C. Government Public Cloud Azure Landing Zone.

## Prerequisites

1. Create a [provisioning request for a Project Set](../../get-started/provision-a-project-set.md) for your team on the B.C. Government Public Cloud.

2. Once approved, your Project Set will be provisioned, followed up by an email sent to the Product Owner and Technical Lead(s) once the provisioning is complete.

3. Learn how to manage user access to Azure by reviewing [User Management in Azure](user-management.md).

## Limitations of the Azure Landing Zone

Take the following into consideration when building your application in the Azure Landing zone:

* There is no direct (private) connectivity to the B.C. Government network. Any application requiring access to data on this network must use a public endpoint.
  * See [Upcoming Features: Express Route](../upcoming-features/express-route.md) for more information.

* Only HTTPS applications that are compatible with public endpoints through [Azure Application Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/overview) are supported.

* The only supported regions are Canada Central (ie. `canadacentral`) and Canada East (ie. `canadaeast`).

* Most networking is under the management of the Azure Landing Zone and is not subject to change.
  * See [Networking within the Azure Landing Zone](networking.md) for more information.

## Next steps

* [Deploy to the Azure Landing Zone](deploy-to-the-azure-landing-zone.md)

## Related pages

* [Provision a Project Set](../../get-started/provision-a-project-set.md)
* [User Management in Azure](user-management.md)
* [Configuring GitHub Action OIDC Authentication to Azure](../best-practices/ci-cd.md#configuring-github-action-oidc-authentication-to-azure)
* [Deploy to the Azure Landing Zone](deploy-to-the-azure-landing-zone.md)
