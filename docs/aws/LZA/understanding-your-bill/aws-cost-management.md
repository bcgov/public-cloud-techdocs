# AWS Cost Management

Last updated: **{{ git_revision_date_localized }}**

!!! warning "Coming Soon"

    The AWS LZA cost management solution powered by QuickSight is not yet available but is coming soon. This documentation is provided in advance of the feature release.

Learn more about managing your AWS spending and resources with our AWS billing and cost management dashboard (link coming soon), featuring the Cloud Intelligence Dashboards and CUDOS (AWS Cost and Usage Dashboards Operations Solution).

Powered by Amazon QuickSight, these tools meticulously analyze your AWS expenditure and resource utilization. They aim to equip you with the insights necessary to make well-informed, data-backed decisions, ultimately helping you streamline your cloud operations and optimize cost efficiency.

Through our deployment of these advanced dashboards, we transform raw data into actionable insights, offering a granular view of your AWS costs and usage patterns.

Explore our comprehensive documentation to discover how to access and leverage these dashboards effectively, enabling you to manage your AWS Landing Zone Accelerator (LZA) environment strategically and sustainably.

Make sure to read the role-based access section below before logging into the AWS billing and cost management dashboard (link coming soon).

## Project set structure

A project set in our AWS Landing Zone Accelerator (LZA) environment consists of 4 AWS accounts, each designated for a specific purpose within the project lifecycle. For example, `tnfhhm` is a project set that includes the following accounts:

1. `tnfhhm-dev` - Development account
2. `tnfhhm-test` - Testing account
3. `tnfhhm-prod` - Production account
4. `tnfhhm-tools` - Tools and utilities account

## Role-based access

Users can access these accounts using predefined AWS roles, each tailored to specific responsibilities and access requirements:

1. **Viewers**
2. **Billing viewers**
3. **Developers**
4. **Admins**
5. **Security auditors**

To access the dashboards, users must be assigned the **billing viewers** role. This role is crucial for accessing detailed billing and cost information across the project set.

## Row-level security implementation

We've deployed a RLS (Row-Level Security) data set in Amazon QuickSight to ensure users see only authorized data. This mechanism is crucial for maintaining data confidentiality and complying with our security standards.

## Data refresh limits and scheduling

It's worth noting that the Enterprise edition of our dashboards has a limitation on data refresh frequency. Specifically, data can be fully refreshed up to 32 times per day. To comply with this limitation and ensure timely data updates, we've scheduled a Lambda function to run every 30 minutes from 8 AM to 5 PM PST, Monday to Friday. This function checks for users with **billing viewers** access in Keycloak and updates the RLS data sheet stored in the S3 bucket.

## Timing and access considerations

Because of this scheduling, there could be a maximum delay of 30 minutes for a user to access the project set data in the dashboard after being assigned the **billing viewers** role through the registry. It's important to keep this timing in mind when anticipating access to the dashboards.

## Accessing multiple project sets

Users with responsibilities across multiple project sets can access and view billing data and dashboards for each set if they have the **billing viewers** role for those specific project sets.

If you need to view billing information for multiple project sets, make sure you're granted the **billing viewers** role for each set you want to monitor.

## Granting access

Users who need access to the dashboards should request the **billing viewers** role. This can be done through the following process:

1. Contact the Product Owner or Technical Lead of your project set
2. Request the **billing viewers** role, providing justification for the need to access the dashboard
3. The Product Owner or Technical Lead will add you to the appropriate Entra ID security group (`DO_PuC_AWS_{LicensePlate}_BillingViewers`) using [Microsoft Account Management](https://myaccount.microsoft.com/groups) or the [Azure portal](https://portal.azure.com)
4. For detailed steps on user role assignment, refer to the [user management documentation](../design-build-deploy/user-management.md)

## Dashboard functionality

The dashboards offer advanced filtering capabilities to tailor the cost and usage data presentation to the user's needs. These filters include:

- **Ministry filters**: Users can filter data at the ministry level, such as CITZ, to view aggregated cost and usage data relevant to their specific ministry
- **Project set filters**: Users can drill down into specific project sets (e.g., `tnfhhm`) to analyze data associated with that set of AWS accounts
- **Account-level filters**: Within a project set, users can further filter data by individual AWS accounts, for example: `tnfhhm-dev`, `tnfhhm-test` to gain insights into the cost and usage patterns of each account

