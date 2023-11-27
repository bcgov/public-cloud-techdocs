# Requirements for building your application

Last updated: **November 27, 2023**

The following sections describe the requirements for building your application on the B.C. Government Public Cloud, AWS Landing Zone.

## Limitations of the AWS Landing Zone

- No direct (private) connectivity to the B.C. Government network. This means any application that requires access to application or data in the B.C. Government network will need to use a public endpoint.
- Only HTTPS applications are supported for public endpoints via the Amazon API Gateway or Application Load Balancer.
<!-- List any other limitations here 

Some suggestions:
- Log groups are protected by the AWS Landing Zone guardrails and cannot be deleted. This can cause issues if they are created by Terraform
- Only the Canada (Central) region is supported (ca-central-1)
- Most networking is managed by the AWS Landing Zone and cannot be changed. Security groups are an exception to this
- IAM Users and their access keys can only be created by the [IAM User management service](iam-user-service.md), created and managed by the Public Cloud team
-->

## Prerequisites

- Create a project set provisioning request for your team on the B.C. Government Public Cloud. For more information, see [Provision a project set](provision-a-project-set.md).
  - Once approved, your project set will be provisioned. An email will be sent to the Product Owner and Technical Lead once provisioning is complete.
- Request access for the reset of the team by submitting a request to <cloud.pathfinder@gov.bc.ca>. For more information, see [Account access](provision-a-project-set.md#account-access)

## Other requirements and best practices

- To use GitHub Actions for deploying your application, OIDC authentication is required. For more information, see [Configuring GitHub Action OIDC Authentication to AWS](deploy-an-app-to-the-aws-landing-zone.md#configuring-github-action-oidc-authentication-to-aws).
<!-- List any other requirements and best practices here

Some suggestions:
- Use a CI/CD pipeline to deploy your application
- Use infrastructure as code, such as Terraform, to deploy your application
- Set up a monitoring solution for your application
- Configure budgets through the Product Registry to receive notifications when your budget is close to being exceeded
- Limit access to your AWS accounts to only those who need it
 -->

## Next steps

- [Deploy an application to the B.C. Government AWS Landing Zone](deploy-an-app-to-the-aws-landing-zone.md)

## Related pages

- [Provision a project set](provision-a-project-set.md)
- [Account access](provision-a-project-set.md#account-access)
- [Configuring GitHub Action OIDC Authentication to AWS](deploy-an-app-to-the-aws-landing-zone.md#configuring-github-action-oidc-authentication-to-aws)
- [Deploy an application to the B.C. Government AWS Landing Zone](deploy-an-app-to-the-aws-landing-zone.md)
