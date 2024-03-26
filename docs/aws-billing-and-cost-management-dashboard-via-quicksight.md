# AWS Billing and Cost Management Dashboards

- Dive into the intricacies of AWS spending and resource management with our AWS Billing and Cost Management Dashboards, featuring the Cloud Intelligence Dashboards and CUDOS (AWS Cost and Usage Dashboards Operations Solution). 

- Powered by Amazon QuickSight, these tools are engineered to deliver a detailed analysis of your AWS expenditure and resource usage. They aim to equip you with the insights needed to make informed, data-backed choices, helping you streamline your cloud operations and enhance cost efficiency.

- Our deployment of these advanced dashboards transforms raw data into actionable insights, offering a granular view of your AWS costs and usage patterns.

- Explore the following documentation to learn how to access and utilize these dashboards to manage your AWS environment strategically and sustainably.

## Project Set Structure

A project set in our AWS environment consists of four AWS accounts, each designated for a specific purpose within the project lifecycle. For example, `tnfhhm` is a project set that includes the following accounts:

1. `tnfhhm-dev` - Development account
2. `tnfhhm-test` - Testing account
3. `tnfhhm-prod` - Production account
4. `tnfhhm-tools` - Tools and utilities account

## Role-Based Access

Users can access these accounts using predefined AWS roles, each tailored to specific responsibilities and access requirements:

1. **Viewers**
2. **Billing Viewers**
3. **Developers**
4. **Admins**
5. **Security Auditors**

To access the dashboards, users must be assigned the **Billing Viewers** role. This role is crucial for accessing detailed billing and cost information across the project set.

## Row-Level Security Implementation
- We've deployed a RLS(Row-Level Security) data set within Amazon QuickSight to ensure that users only see the data they're authorized to view. This mechanism is crucial for maintaining data confidentiality and compliance with our security standards.

## Data Refresh Limits and Scheduling
- It's important to note that the Enterprise edition of our dashboards has a limitation on data refresh frequency. Specifically, the data can be fully refreshed up to 32 times per day. To adhere to this limitation and ensure timely data updates, we've scheduled a Lambda function to run every 30 minutes between 8 AM to 5 PM, Monday to Friday. This function checks for users with Billing Viewer access in Keycloak and updates the RLS data sheet stored in the S3 bucket.

## Timing and Access Considerations
- Due to this scheduling, there might be a maximum of 30-minute delay for a user to view the project set data in the dashboard after being assigned the Billing Viewer role through the registry. It's essential to consider this timing when expecting access to the dashboards.

# Accessing Multiple Project Sets
- Users with responsibilities across multiple project sets can access and view billing data and dashboards for each set, provided they have the Billing Viewers role for those respective project sets.

- If you need to view billing information for multiple project sets, ensure you are granted the Billing Viewers role for each set you wish to monitor.

## Granting Access

Users who need access to the dashboards should request the **Billing Viewers** role. This can be done through the following process:

1. Contact the product owner or technical lead of your project set.
2. Request the **Billing Viewers** role, providing justification for the need to access the dashboard.
3. The product owner or technical lead will grant the appropriate role using the user management functionality available at [BC Developer Registry](https://registry.developer.gov.bc.ca/public-cloud/products/all).
4. For detailed steps on user role assignment, refer to the [User Management Documentation](https://developer.gov.bc.ca/docs/default/component/public-cloud-techdocs/user-management/).


## Dashboard Functionality

The dashboards offer advanced filtering capabilities to tailor the cost and usage data presentation to the user's needs. These filters include:

- **Ministry Filters**: Users can filter data at the ministry level, such as CITZ, to view aggregated cost and usage data relevant to their specific ministry.
- **Project Set Filters**: Users can drill down into specific project sets (e.g., `tnfhhm`) to analyze data associated with that set of AWS accounts.
- **Account-Level Filters**: Within a project set, users can further filter data by individual AWS accounts (e.g., `tnfhhm-dev`, `tnfhhm-test`) to gain insights into the cost and usage patterns of each account.

## Conclusion

By following the outlined process to obtain the necessary role and using the dashboard's filtering capabilities, users can effectively monitor and analyze AWS cost and usage data. This structured approach ensures both security and granularity in accessing and interpreting cloud resource data.
