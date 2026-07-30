# Azure Front Door

Last updated: **{{ git_revision_date_localized }}**

Azure Front Door is a global entry point that uses the Microsoft edge network to deliver fast, secure, and highly scalable web applications. It provides dynamic site acceleration (DSA), SSL offloading, global load balancing with instant failover, and application layer security.

## Security requirements

The following security settings are **required** in Landing Zones:

- Azure Front Door profiles must use the **Premium tier** that supports managed Web Application Firewall (WAF) rules and private link
- Azure Front Door profiles must use a **minimum TLS version of 1.2**
- WAF on Azure Front Door must have **request body inspection** enabled
- WAF must be enabled for Azure Front Door **entry-points**
- **Bot Protection** must be enabled for Azure Front Door WAF
- WAF **rate limit rule** for Azure Front Door must be enabled
- Secure **private connectivity** between Azure Front Door Premium and Azure Storage Blob or Azure App Service must be configured
- WAF must use the specified **mode** (i.e. **Prevention**) for Azure Front Door Service

## Deployment failures due to policy violation

The Landing Zones have several policies that enforce the use of security best practices for Azure Front Door. When creating a Front Door, although the portal UI may report that validation passes, the deployment may fail due to policy violations.

From our experience, the deployment failure shows a generic message: "_Deployment validation failed. Additional details from the underlying API that might be helpful: The template deployment failed because of policy violation. Please see details for more information._"

Unfortunately, to determine the actual policy violation that caused the deployment failure, you have to look at the Activity Logs, then look at the JSON to find out which policy it was.

## Web Application Firewall (WAF) Policy

When you create a WAF policy through the Front Door create UI, you are limited to a subset of the available settings. For example, you cannot enable **request body inspection** or configure **bot protection** rulesets through the UI.

To create a WAF policy with all the required security settings, you must create the policy **independently** and then associate it with your Front Door.
