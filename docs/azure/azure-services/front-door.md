# Azure Front Door

Last updated: **{{ git_revision_date_localized }}**

Azure Front Door is a global, scalable entry point that uses the Microsoft global edge network to create fast, secure, and widely scalable web applications. It provides dynamic site acceleration (DSA), SSL offloading, global load balancing with instant failover, and application layer security.

## Deployment failures due to policy violation

The Landing Zones have several policies that enforce the use of security best practices for Azure Front Door. When creating a Front Door, although the portal UI may report that validation passes, the deployment may fail due to policy violations.

From our experience, the deployment failure shows a generic message: "_Deployment validation failed. Additional details from the underlying API that might be helpful: The template deployment failed because of policy violation. Please see details for more information._"

Unfortunately, to determine the actual policy violation that caused the deployment failure, you have to look at the Activity Logs, and then look at the JSON to find out which policy it actually was.

## Web Application Firewall (WAF) Policy

When creating a WAF policy through the Front Door create UI, you are limited to a subset of the available settings. For example, you cannot enable **request body inspection** or configure **bot protection** rulesets through the UI.

Therefore, to create a WAF policy with all the required security settings, you must create the WAF policy **independently** and then associate it with your Front Door.
