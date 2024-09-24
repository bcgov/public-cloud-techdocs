# User Management in the Azure Landing Zone

Last updated: **September 24, 2024**

This guide provides an overview of user management in the Azure landing zone, specifically tailored for Product Owners (POs) and Technical Leads who have been granted a restricted Owner role on the project set management group. This role is inherited on the subscription within the project set.

## What You Can Do

As a Product Owner or Technical Lead with restricted Owner permissions, you have the ability to:

1. Assign users to roles at various levels:
   - Project set management group (prefixed with your project-set license plate, e.g., "abc123")
   - Subscription (prefixed with your project-set license plate, e.g., "abc123")
   - Resource groups
   - Individual resources

2. Create custom roles

3. Create and manage service principals

4. Create and manage managed identities

5. Create and manage all resources within your subscription

## What You Can't Do

As a Product Owner or Technical Lead with restricted Owner permissions, you do not have the ability to:

1. Assign users to an Owner role.

## Best Practices for User Management

To ensure secure and efficient user management, we recommend the following best practices:

1. **Assign roles at higher levels**: Whenever possible, assign users to roles at the management group or subscription levels. This approach simplifies management and provides consistent access across resources.

2. **Follow the principle of least privilege**: Only give users the roles they need to perform their specific job functions. This minimizes potential security risks.

3. **Regularly review access**: Periodically review user access and remove unnecessary permissions to maintain a secure environment.

## How to Manage Users in Azure Portal

To manage users and their roles:

1. Log in to the [Azure Portal](https://portal.azure.com)

2. Navigate to your subscription or management group (remember, these are prefixed with your project-set license plate, e.g., "abc123")

3. In the left sidebar, click on "Access control (IAM)"

4. Use the "Add" button to assign new roles to users

5. Use the "Role assignments" tab to view and manage existing role assignments

6. To create custom roles, use the "Roles" tab and click "Add custom role"

Remember, user management is a critical aspect of maintaining a secure and well-organized Azure environment. Always double-check your assignments and follow your organization's security policies.

For more detailed instructions on specific tasks or advanced user management techniques, please refer to the [official Azure documentation](https://docs.microsoft.com/en-us/azure/role-based-access-control/).

## Note on Project-Set License Plates

Your subscriptions and management groups are prefixed with your unique project-set license plate (e.g., "abc123"). This prefix helps identify and organize resources specific to your project. When navigating the Azure portal or assigning roles, always look for resources and groups that start with your project-set license plate.
