# Requirements for building your application in Azure

Last updated: **{{ git_revision_date_localized }}**

The following sections describe the requirements for building your application on the B.C. government Public cloud Azure Landing Zone.

## Prerequisites

1. Create a [provisioning request for a Project Set](../../welcome/provision-a-project-set.md) for your team on the B.C. government Public cloud.

2. Once approved, your Project Set will be provisioned, followed up by an email sent to the Product Owner and Technical Lead(s) once the provisioning is complete.

3. Learn how to manage user access in Azure by reviewing [User Management in Azure](user-management.md).

## Limitations of the Azure Landing Zone

Take the following into consideration when building your application in the Azure Landing zone:

- Direct (private) connectivity to the B.C. government network is supported through [ExpressRoute](./networking-express-route.md). However, any application requiring access to data hosted on-premises will need to submit a firewall request.
  - See the [On-premises connectivity](./networking-express-route.md) for more information.

- Only HTTPS applications that are compatible with public endpoints through [Azure Application Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/overview) are supported.

- The only supported regions are Canada Central and Canada East.

- Most networking is under the management of the Azure Landing Zone and is not subject to change.
  - See [Networking within the Azure Landing Zone](networking.md) for more information.

## Next steps

- [Deploy to the Azure Landing Zone](deploy-to-the-azure-landing-zone.md)

## Related pages

- [Provision a Project Set](../../welcome/provision-a-project-set.md)
- [User Management in Azure](user-management.md)
- [Configuring GitHub Action OIDC Authentication to Azure](../best-practices/iac-and-ci-cd.md#configuring-github-action-oidc-authentication-to-azure)
- [Deploy to the Azure Landing Zone](deploy-to-the-azure-landing-zone.md)
