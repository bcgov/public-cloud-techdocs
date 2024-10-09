# AWS Security & Compliance Guardrails

Last updated: **September 26, 2024**

As a user of our AWS environment, it's important to understand the restrictions and guidelines in place to ensure security and compliance. This document outlines the key points you need to know when working with your AWS account.

## Supported Regions

You can only use AWS services in the following regions:

- Canada (Central) - ca-central-1
- US East (N. Virginia) - us-east-1

Most actions and resource creations outside these regions will be blocked. This means:

- You must deploy all your applications and services in these two regions.
- Traditional multi-region architectures for disaster recovery or global applications may not be possible. Discuss alternatives with the central team for critical applications that might need such capabilities.
- While most services are restricted to these regions, some global services (like IAM, CloudFront, Route 53) can still be used, although your actions within these services might be limited.

## Protected Resources

Some resources in your account are managed by our central team and are protected from modification. You can identify these resources by:

- Names beginning with "PBMM"
- The tag "Accelerator: PBMM"

You cannot modify, delete, or in some cases even interact with these protected resources. This includes items such as certain CloudFormation stacks, IAM roles, S3 buckets, and network components.

This means:

- If your application needs to interact with protected resources (prefixed with PBMM), you may need to request special permissions or assistance from the central team.
- You can't modify the encryption settings of existing PBMM-tagged storage resources.
- While you can view security findings from services like GuardDuty or Security Hub, you might not be able to dismiss or modify these findings directly if they're related to protected resources.

## Network and Infrastructure

- You cannot create, modify, or delete core networking components like VPCs, subnets, internet gateways, and NAT gateways.
- You cannot create or modify VPC endpoints, except for the PrivateLink endpoints for API Gateway.
- You can add routes to existing routes tables, but you cannot modify or delete the routes. So be cautious when adding routes.

This means:

- You can't create new VPCs or modify existing ones that are part of the protected infrastructure. Plan your resource deployments within the existing network structure.
- Resources deployed in your VPCs are not directly accessible from the internet. If your application requires internet access, you'll need to use an API Gateway with PrivateLink or route your traffic through the landing zone's perimeter network using a public ALB. For more information, see [Exposing Services to the Internet](../design-build-and-deploy-an-application/networking.md#exposing-services-to-the-internet).

## Security and Compliance

1. Encryption:
   - Encryption is mandatory for services like EBS volumes, RDS instances, and EFS file systems.
   - You cannot disable encryption on resources that require it.

   This means:

   - When creating new S3 buckets, EBS volumes, or RDS instances, you must ensure they are encrypted. The system will enforce this, but be aware that you can't create unencrypted storage resources.

2. Security Services:
   - You have limited ability to modify settings for services like GuardDuty, Security Hub, and Macie.

3. Logging and Monitoring:
   - You cannot modify or delete CloudWatch logs, alarms, and dashboards related to our managed infrastructure.
   - You can create your own CloudWatch alarms and dashboards, but you can't modify ones that are part of the protected infrastructure.

## Account Management

- You cannot perform high-level account actions such as leaving the AWS organization or closing the account.
- Creation of new IAM users and groups is restricted. A limited custom service is deployed in your accounts to create IAM users. See [IAM User Service](./iam-user-service.md) for more information.

Implications:

- You can't create new IAM users or groups. If you need to onboard new team members or create new roles, you can do that using the [Product Registry](https://registry.developer.gov.bc.ca). See [BC Gov's Product Registry - User management documentation](./user-management.md) for more information.
- Be cautious when attaching policies that grant broad permissions. Use the least privilege principle when assigning permissions.

## Service Restrictions

- Access to AWS Marketplace is limited. Please contact the central team if you need software or services from the Marketplace.

Implications:

- Some AWS services might be entirely restricted. Always check if you can access a service before planning to use it in your projects.
- For services that are available, you might find that certain actions within those services are restricted.
- If you need specific software or tools from AWS Marketplace, you'll need to request it through the central team. Plan ahead for any software needs in your projects.

## Cost Management

- You do not have direct access to billing information or the ability to set up detailed cost allocation tags. However, you can view your account's total spend and associated costs using the AWS Cost Explorer service.
- Budgets and notifications have been pre-configured for your accounts to alert you when your spend is approaching limits. The values are configured in the [Product Registry](https://registry.developer.gov.bc.ca). You may also configure additional budgets and alerts as needed.
- In order to provide a centralized view of costs across all accounts and projects, the Public Cloud team has created a centralized Cost Explorer dashboard. This dashboard is used to track and analyze costs for all projects and accounts. See [AWS billing and cost management dashboards](../understanding-your-aws-bill/aws-billing-and-cost-management-dashboard-via-quicksight.md) for more information.

By following these guidelines, you help maintain the security and compliance of our AWS environment. Remember, these restrictions are in place to maintain a secure and compliant environment. If you find that these limitations significantly impact your work, don't hesitate to discuss your needs with the Public Cloud team. They can provide guidance, suggest workarounds, or, if necessary, help you request exceptions for critical business needs.

If you have any questions or need assistance, please contact the Public Cloud team.
