# Azure Front Door

Last updated: **{{ git_revision_date_localized }}**

Azure Front Door is a global entry point that uses the Microsoft edge network to deliver fast, secure, and highly scalable web applications. It provides dynamic site acceleration (DSA), SSL offloading, global load balancing with instant failover, and application layer security.

---

## Security requirements

The following security settings are **required** in Landing Zones:

- Azure Front Door profiles may use the **Standard** or **Premium** tier
- Azure Front Door profiles must use a **minimum TLS version of 1.2**
- WAF on Azure Front Door must have **request body inspection** enabled
- WAF must be enabled for Azure Front Door **entry-points**
- **Bot Protection** must be enabled for Azure Front Door Premium WAF
- Azure Front Door Premium WAF policies must use the **Microsoft Default Rule Set (DRS) version 2.2**
- Secure **private connectivity** between Azure Front Door (when using the **Premium** tier) and Azure Storage Blob or Azure App Service must be configured
- WAF must use the specified **mode** (i.e. **Prevention**) for Azure Front Door Service

The `Azure Front Door WAF policy should use Microsoft Default Rule Set` Azure Policy enforces DRS 2.2. The managed rule set type is `Microsoft_DefaultRuleSet`, and the rule set version is `2.2`.

!!! note "Front Door Standard"
    Front Door Standard remains permitted. It supports custom WAF rules only.

    Front Door Standard cannot use DRS 2.2, Bot Protection, or other managed rule sets. The managed rule set requirements apply to Front Door Premium.

!!! warning "Update existing WAF policies"
    New and existing Front Door Premium WAF policies must use DRS 2.2. A deployment fails when a Premium policy uses another managed rule set or version.

DRS 2.2 includes Microsoft threat intelligence protections and updates based on the OWASP Core Rule Set. Review your rule overrides, exclusions, and Paranoia Level settings when you change rule set versions. DRS 2.2 uses Paranoia Level 1 rules by default.

Use the [Front Door managed rule set upgrade guide](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/waf-front-door-drs#upgrading-or-changing-ruleset-version) to upgrade an existing WAF policy. Review your exclusions and custom rule actions after the upgrade. DRS 2.2 changes some rule group names and disables Paranoia Level 2 rules by default.

Review the [rules included in the Default Rule Set 2.2](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/waf-front-door-drs).

## Deployment failures due to policy violation

The Landing Zones have several policies that enforce the use of security best practices for Azure Front Door. When creating a Front Door, although the portal UI may report that validation passes, the deployment may fail due to policy violations.

From our experience, the deployment failure shows a generic message: "_Deployment validation failed. Additional details from the underlying API that might be helpful: The template deployment failed because of policy violation. Please see details for more information._"

Unfortunately, to determine the actual policy violation that caused the deployment failure, you have to look at the Activity Logs, then look at the JSON to find out which policy it was.

## Web Application Firewall policy

When you create a WAF policy through the Front Door create UI, you are limited to a subset of the available settings. For example, you cannot enable **request body inspection** or configure **bot protection** rulesets through the UI.

To create a WAF policy with all the required security settings, you must create the policy **independently** and then associate it with your Front Door.

## Related pages

- [Rules included in Front Door Default Rule Set 2.2](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/waf-front-door-drs)
- [Upgrade a Front Door WAF rule set](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/waf-front-door-drs#upgrading-or-changing-ruleset-version)
- [Create a WAF policy for Azure Front Door](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/waf-front-door-create-portal)
