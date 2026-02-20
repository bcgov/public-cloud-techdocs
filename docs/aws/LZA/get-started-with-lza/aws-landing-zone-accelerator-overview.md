# AWS Landing Zone Accelerator overview

Last updated: **{{ git_revision_date_localized }}**

The AWS Landing Zone Accelerator (LZA) is B.C. government's current solution for secure, compliant AWS cloud environments. LZA provides a standardized, well-architected foundation for building and deploying applications in AWS.

## What is LZA?

The AWS Landing Zone Accelerator is a comprehensive solution that provides:

- **Multi-account structure**: Separate development, test, production, and tools environments
- **Security by design**: Built-in security guardrails and compliance controls
- **Centralized identity management**: Integration with Entra ID for user access control
- **Cost management**: Built-in billing and cost monitoring capabilities
- **Networking foundation**: Pre-configured VPCs and networking architecture

## Learn more about AWS LZA

For complete technical documentation and deployment guides:

- **Official AWS solution page**: [Landing Zone Accelerator on AWS](https://aws.amazon.com/solutions/implementations/landing-zone-accelerator-on-aws/)
- **Open source repository**: [AWS Landing Zone Accelerator on GitHub](https://github.com/awslabs/landing-zone-accelerator-on-aws)

## Your project set structure

When you're provisioned in LZA, you receive a project set consisting of four AWS accounts:

```
Project Set (abc123)
├── Development Account (abc123-dev)
├── Test Account (abc123-test)
├── Production Account (abc123-prod)
└── Tools Account (abc123-tools)
```

Each account serves a specific purpose in your application lifecycle, providing clear separation between environments.

## Accessing your AWS accounts

Once your project set is provisioned and you have the appropriate permissions, you can access your AWS accounts through the AWS SSO Portal:

**🔗 [Access AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts)**

### First-time access

1. Navigate to the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts)
2. Sign in with your IDIR credentials
3. You'll see a list of available AWS accounts based on your assigned roles
4. Click on the account you want to access
5. Choose your role (if you have multiple roles assigned)
6. You'll be redirected to the AWS Management Console

### User roles and access

LZA uses five predefined roles with different levels of access:

- **Viewers**: Read-only access to AWS resources
- **Developers**: Administrative access to dev, test, and tools accounts
- **Admins**: Full access to all accounts including production
- **Billing viewers**: Access to cost and billing information
- **Security auditors**: Access to security configurations and audit logs

For detailed information about user management and permissions, see our [user management guide](../design-build-deploy/user-management.md).

## Key features and benefits

### Security and compliance

- Pre-configured security guardrails prevent common misconfigurations
- Automated compliance monitoring and reporting
- Integration with B.C. government identity management systems
- Comprehensive audit logging and monitoring

### Simplified management

- Standardized account structure across all project sets
- Centralized user access management through Entra ID security groups
- Pre-built networking and security configurations

### Development-friendly

- Separate environments for development, testing, and production
- Tools account for CI/CD pipelines and shared resources
- Pre-configured networking allowing secure communication between accounts
- Support for modern development practices and infrastructure as code

## Getting help and support

- **Documentation**: Complete guides available in this documentation site
- **Service Desk**: [Public Cloud Service Desk (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3) (preferred method)
- **Other support channels**: See our [support page](../../../welcome/support.md) for all available support options and contact methods

## Next steps

1. **Get familiar with your accounts**: Log into the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts) and explore your project set
2. **Review security guardrails**: Understand the [compliance and security controls](guardrails.md) in place
3. **Plan your deployment**: Review [requirements](../design-build-deploy/requirements.md) and [deployment guidance](../design-build-deploy/deploy-to-the-aws-landing-zone-accelerator.md)
4. **Set up user access**: Configure [user management](../design-build-deploy/user-management.md) for your team
5. **Explore best practices**: Review our [guidelines](../best-practices/be-mindful.md) for building on LZA

The LZA platform is designed to accelerate your cloud adoption while maintaining the highest standards of security and compliance required for B.C. government services.
