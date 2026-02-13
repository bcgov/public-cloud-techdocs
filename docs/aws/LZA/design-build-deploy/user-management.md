# User management in the AWS Landing Zone Accelerator

Last updated: **{{ git_revision_date_localized }}**

This guide explains how to manage user access in your AWS Landing Zone Accelerator (LZA). It's designed for Product Owners (POs) and Technical Leads (TLs) who need to administer user permissions within their project set. LZA uses Entra ID security groups for access management, similar to the Azure platform.

## Understanding your project set

Your project set consists of four AWS accounts grouped within your organizational structure. Each account serves a specific purpose within the project lifecycle:

```
Project Set (abc123)
├── Development Account (abc123-dev)
├── Test Account (abc123-test)
├── Production Account (abc123-prod)
└── Tools Account (abc123-tools)
```

All your AWS resources are organized within these accounts, and your unique project set license plate (e.g., "abc123") prefixes all your accounts.

## Access management overview

### User roles and permissions

LZA provides five predefined user roles, each with specific AWS permissions:

1. **Viewers** - Read-only access to AWS resources
2. **Billing viewers** - Access to AWS cost and billing information
3. **Developers** - Administrative access to dev, test, and tools accounts
4. **Admins** - Full administrative access to all accounts
5. **Security auditors** - Access to security configuration and audit logs

### Entra ID security group structure

Access is managed through Entra ID security groups that follow this naming pattern:

`DO_PuC_AWS_{LicensePlate}_{Role}`

For example:

- `DO_PuC_AWS_abc123_Admins`
- `DO_PuC_AWS_abc123_Developers`
- `DO_PuC_AWS_abc123_Viewers`
- `DO_PuC_AWS_abc123_BillingViewers`
- `DO_PuC_AWS_abc123_SecurityAuditors`

These security groups are synchronized with AWS IAM roles to provide the appropriate access levels across your project set accounts.

## Important limitation: Nested groups are not supported

AWS IAM Identity Center (SSO) does **not** support *nested groups* (a security group added as a member of another security group).  

### What this means

- Users must be added **directly** to your project set groups (e.g., `DO_PuC_AWS_abc123_Admins`)
- If nested groups are used, users who are only members of the “child group” will **not** receive the expected AWS access, even though it appears correct in Entra.
- Add users directly to the required AWS access groups,this ensures SCIM provisioning and AWS access assignments remain predictable.

## Role-based access details

### Admins

- **AWS Policy**: `arn:aws:iam::aws:policy/AdministratorAccess`
- **Access**: Full administrative access to all four AWS accounts (dev, test, prod, tools)
- **Use case**: Product Owners, Technical Leads, and senior team members who need complete control

### Developers

- **AWS Policy**: `arn:aws:iam::aws:policy/AdministratorAccess`
- **Access**: Administrative access to dev, test, and tools accounts
- **Production access**: Requires Admin role for production account access
- **Use case**: Development team members who need to deploy and manage applications in non-production environments

### Viewers

- **AWS Policy**: `arn:aws:iam::aws:policy/ReadOnlyAccess`
- **Access**: Read-only access to all AWS accounts
- **Use case**: Stakeholders, auditors, or team members who need visibility but not modification rights

### Security auditors

- **AWS Policy**: `arn:aws:iam::aws:policy/SecurityAudit`
- **Access**: Security configuration and audit log access across all accounts
- **Use case**: Security team members, compliance officers, and auditors

### Billing viewers

- **Custom AWS Policy**: Access to AWS cost management and billing features
- **Access**: AWS Cost Explorer, budgets, and billing dashboard access
- **Policy JSON**:

```json
{
  "Statement": [
    {
      "Action": "ce:*",
      "Effect": "Allow",
      "Resource": "*",
      "Sid": "AllowCostExplorer"
    },
    {
      "Action": "budgets:*",
      "Effect": "Allow",
      "Resource": "*",
      "Sid": "AllowBudgets"
    }
  ],
  "Version": "2012-10-17"
}
```

## Managing security group membership

As a Product Owner or Technical Lead, you can manage your project set's security groups using two methods:

### Option 1: Using Microsoft Account Management

1. Go to [Microsoft Account Groups](https://myaccount.microsoft.com/groups)
2. Sign in with your Microsoft account
3. You'll see a list of groups you own or are a member of
4. For groups you own, you can:
   - Add or remove members
   - View group details
   - Manage group settings

### Option 2: Using the Azure Portal

1. Log in to the [Azure portal](https://portal.azure.com)
2. Navigate to Entra ID
3. Go to "Groups" in the left sidebar
4. Find and select your security group (prefixed with `DO_PuC_AWS_{LicensePlate}`)
5. Use the "Members" tab to add or remove users

## Prerequisites for user access

- Users must have a valid IDIR (Integrated Directory) account
- Users must be added to the appropriate Entra ID security group
- Only Product Owners or Technical Leads can manage user permissions

## Accessing your AWS accounts

Once users are added to the appropriate security groups, they can access the AWS accounts through:

1. **AWS SSO Portal**: [https://bcgov.awsapps.com/start/#/?tab=accounts](https://bcgov.awsapps.com/start/#/?tab=accounts)
2. Users with multiple roles can choose their desired role when logging in
3. Access is automatically granted based on Entra ID group membership

## Best practices for user management

1. **Follow the principle of least privilege**: Only assign users to the minimum role they need

   - Use Viewers for stakeholders who only need visibility
   - Use Developers for team members working in non-production environments
   - Reserve Admins for Product Owners, Technical Leads, and senior team members

2. **Regularly review and audit access**:

   - Periodically review security group memberships
   - Remove users who no longer need access
   - Audit role assignments across all accounts

3. **Use appropriate roles for specific needs**:

   - **Billing viewers** for financial oversight and cost management
   - **Security auditors** for compliance and security reviews
   - **Developers** for application development and testing
   - **Admins** for infrastructure management and production deployments

4. **Document your access structure**:

   - Maintain clear documentation of who has access to what
   - Record the business justification for each role assignment
   - Keep track of temporary access grants

5. **Coordinate with your team**:
   - Communicate role changes to affected team members
   - Ensure critical roles have backup assignees
   - Plan for personnel changes and access transitions

## Security considerations

- **Entra ID integration**: LZA leverages Entra ID for centralized identity management
- **Multi-factor authentication**: Ensure all users have MFA enabled on their IDIR accounts
- **Access logging**: All AWS actions are logged and auditable through CloudTrail
- **Role separation**: Production access requires explicit Admin role assignment
- **Automatic provisioning**: Group membership changes are automatically synchronized with AWS

## Troubleshooting access issues

If users experience access problems:

1. **Verify group membership**: Ensure the user is in the correct Entra ID security group
2. **Check synchronization**: Allow up to 40 minutes for group changes to propagate to AWS
3. **Validate IDIR status**: Confirm the user's IDIR account is active and properly configured
4. **Review role requirements**: Ensure the user has the appropriate role for their intended actions

## Need help?

If you need assistance with user management or have questions about your access structure:

- Contact the Public Cloud team through [BC Gov's Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3)
- Refer to the [Platform Product Registry](https://registry.developer.gov.bc.ca/) for managing the Product Owner and Technical Leads
- Consult the [AWS IAM documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/) for advanced AWS permissions concepts

