# AWS security and compliance guardrails

Last updated: **{{ git_revision_date_localized }}**

This document explains the security and compliance guardrails implemented in the AWS Landing Zone Accelerator (LZA). These guardrails ensure our cloud environment remains secure, compliant with standards like **CCCS Medium [Canada Federal PBMM]**, and cost-effective while maintaining consistency across all workloads.

!!! info "Understanding Guardrails"
    Guardrails are automated security and compliance controls that help prevent misconfigurations and ensure best practices. They don't require extra effort from you most of the time, but they may prevent actions that could compromise security or compliance.

## Key areas covered

1. [**Supported Regions:**](#supported-regions) Where you can deploy resources
2. [**Protected Resources:**](#protected-resources) Platform-managed components you cannot modify
3. [**Network and Infrastructure:**](#network-and-infrastructure) Networking limitations and requirements
4. [**Security and Compliance:**](#security-and-compliance) Mandatory security configurations
5. [**Account Management:**](#account-management) User and access restrictions
6. [**Service Restrictions:**](#service-restrictions) Limited or blocked AWS services
7. [**Cost Management:**](#cost-management) Billing and budget controls

## Supported regions

You can only deploy AWS resources in the following regions:

- **Canada (Central)** - `ca-central-1` (primary)

!!! warning "Regional Restrictions"
    Most actions and resource creation outside these regions will be **blocked**. Plan all deployments within these approved regions.

### Implications

- **Deploy all applications** in Canada Central for optimal performance and compliance
- **Multi-region architectures** for disaster recovery may be limited - discuss alternatives with the Public Cloud team for critical applications
- **Global services** (IAM, CloudFront, Route 53) remain available

## Protected resources

Platform-managed resources are protected from modification to maintain security and compliance. You can identify protected resources by:

- **Names beginning with** `AWSAccelerator`
- **Tags containing** `Accelerator: AWSAccelerator` or similar platform identifiers

!!! danger "Protected Resource Restrictions"
    You **cannot** change, delete, or interact with protected resources including CloudFormation stacks, IAM roles, S3 buckets, and network components.

### What this means

- **Application integration** with protected resources requires requesting permissions from the Public Cloud team
- **Encryption settings** on Accelerator-tagged storage resources cannot be modified
- **Security findings** from GuardDuty or Security Hub may be view-only for protected resources

## Network and infrastructure

### VPC and Subnet Restrictions

- **No VPC creation** - VPCs and subnets are platform-managed
- **No subnet modification** - Cannot create, modify, or delete subnets
- **No core networking changes** - Internet gateways, NAT gateways, and route tables are protected
- **Security groups** - You can create new security groups for network security, but platform-created security groups with Accelerator tags cannot be modified

!!! info "Network Architecture"
    LZA uses a hub-and-spoke architecture with complete VPC isolation. See our [networking documentation](../design-build-deploy/networking.md) for detailed information.

### Internet access limitations

- **No direct internet access** - All ingress and egress traffic goes through the perimeter firewall; ingress uses the public ALB service
- **Public exposure options** - Use API Gateway or Application Load Balancer for internet-facing services; see [Application Load Balancer Public Routing](../design-build-deploy/networking.md#application-load-balancer-public-routing) for configuration details
- **VPC endpoints** - Limited to specific services; cannot create custom VPC endpoints

### Route table restrictions

- **Can add routes** to existing route tables
- **Cannot modify or delete** existing routes
- **Exercise caution** when adding routes to avoid conflicts

## Security and compliance

### Encryption requirements

!!! warning "Mandatory Encryption"
    Encryption is **required** for all storage services and cannot be disabled.

- **EBS volumes** - Must be encrypted at rest
- **RDS instances** - Encryption at rest mandatory
- **EFS file systems** - Encryption required
- **S3 buckets** - Server-side encryption enforced

### Security services

- **GuardDuty** - Limited ability to modify settings
- **Security Hub** - Findings may be read-only for protected resources
- **AWS Macie** - Configuration managed by platform team

### Logging and monitoring

- **CloudWatch logs** - Cannot modify or delete platform-managed logs
- **CloudWatch alarms** - Can create your own, cannot modify protected alarms
- **AWS CloudTrail** - Logging configuration is protected

!!! tip "Custom Monitoring"
    You can create your own CloudWatch alarms and dashboards for application-specific monitoring while respecting protected infrastructure.

## Account management

### IAM Restrictions

- **No IAM user creation** - Use the [IAM User Service](../design-build-deploy/iam-user-service.md) for programmatic access
- **No IAM group creation** - User management through [Entra ID security groups](../design-build-deploy/user-management.md)
- **Limited policy attachment** - Follow least privilege principles

!!! info "User Management"
    All user access is managed through Entra ID security groups with standardized naming: `DO_PuC_AW_{LicensePlate}_{Role}`. See our [user management guide](../design-build-deploy/user-management.md) for details.

### Account Actions

- **Cannot leave organization** - Account is permanently part of the AWS Organization
- **Cannot close account** - Account closure must be requested through the [Platform Product Registry](https://registry.developer.gov.bc.ca/) by the project owner
- **Limited billing access** - Cost visibility through AWS Cost Explorer only

## Service restrictions

### AWS Marketplace

!!! warning "Marketplace Access"
    Access to AWS Marketplace is **limited**. Contact the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3) for software or service requests.

### Service Availability

- **Some services blocked** - Verify service availability before planning deployments
- **Action restrictions** - Available services may have limited functionality
- **Request process** - Submit requests for additional services through support

### AI/ML Services

- **Limited model access** - Only Amazon Titan and Mistral models available
- **No public marketplace** - Cannot access public AI model marketplace
- **Regional availability** - AI services limited to Canada Central region

For detailed AI service guidance, see [AWS AI Services](../best-practices/aws-ai.md).

## Cost management

### Billing Access

- **No direct billing access** - Cannot view detailed billing information
- **Cost Explorer available** - Can view total spend and cost breakdowns
- **Pre-configured budgets** - Alerts set up based on Platform Product Registry values, but teams can create their own additional budgets

!!! info "Cost Monitoring"
    While detailed billing is restricted, you can monitor costs through AWS Cost Explorer and receive budget alerts when approaching spending limits.

### Budget Management

- **Platform-managed budgets** - Initial budgets configured by platform team
- **Additional budgets allowed** - Can create supplementary budgets and alerts


## Compliance and best practices

### Following Guidelines

By adhering to these guardrails, you help maintain:

- **Security posture** - Consistent security across all workloads
- **Compliance standards** - CCCS Medium and other regulatory requirements
- **Cost optimization** - Preventing unnecessary expenses
- **Operational efficiency** - Standardized approaches across teams

### Getting Help

If these limitations significantly impact your work:

1. **Review documentation** - Check our [requirements](../design-build-deploy/requirements.md) and [best practices](../best-practices/be-mindful.md)
2. **Contact support** - Create a [Support Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3)
3. **Request exceptions** - Work with the Public Cloud team for critical business needs

## Related documentation

- [Requirements](../design-build-deploy/requirements.md) - Platform limitations and constraints
- [Networking](../design-build-deploy/networking.md) - Network architecture and restrictions
- [User Management](../design-build-deploy/user-management.md) - Access control and permissions
- [Be Mindful](../best-practices/be-mindful.md) - Important considerations for LZA
- [Governance](../best-practices/governance.md) - Managing compliance and security
