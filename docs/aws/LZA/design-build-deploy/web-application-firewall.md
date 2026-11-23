# Web application firewall (WAF) in AWS LZA

Last updated: **{{ git_revision_date_localized }}**

AWS Web Application Firewall (WAF) inspects HTTP and HTTPS requests to help protect web applications from common attacks.
This guide explains the central baseline and your options for application rules in AWS Landing Zone Accelerator (LZA).

---

## How WAF works in AWS LZA

The Public Cloud team manages central WAF policies through AWS Firewall Manager in the network account.
These policies define the baseline protections and the resources they cover.

AWS WAF applies protections through a **web access control list (web ACL)**.
A web ACL contains rules and rule groups, and you associate it with the resource you want to protect.
A rule group contains a reusable collection of rules.

Central policies manage web ACLs across workload accounts. Each resource can have only one associated web ACL.
Your baseline and application rules must work together within that web ACL.

For resources within a central policy's scope:

- **No associated web ACL:** Firewall Manager automatically associates a centrally managed web ACL with the resource.
- **Existing team-managed web ACL:** The central policy leaves your web ACL and its association unchanged.
  Your team must ensure that the web ACL includes the baseline.

Automatic association and replacement of existing web ACLs are separate Firewall Manager controls.
AWS LZA uses automatic remediation to associate the baseline web ACL only where a resource has no web ACL.
Replacement of existing team-managed web ACLs is disabled.
See [Firewall Manager policy actions](https://docs.aws.amazon.com/waf/latest/developerguide/create-policy.html#creating-firewall-manager-policy-for-waf).

## Central policy coverage

AWS LZA already deploys central baseline policies for these resources:

| Resource | WAF scope | WAF API Region |
| --- | --- | --- |
| Application Load Balancer (ALB) | `REGIONAL` | Canada (Central), `ca-central-1` |
| Amazon API Gateway REST API stages | `REGIONAL` | Canada (Central), `ca-central-1` |
| Amazon CloudFront distributions | `CLOUDFRONT` | US East (N. Virginia), `us-east-1` |

The global CloudFront policy provides the same five baseline managed rule groups as the regional policies.
CloudFront WAF operations use `us-east-1`, even when your application's regional resources use `ca-central-1`.
See [AWS WAF resource associations and scope](https://docs.aws.amazon.com/waf/latest/developerguide/web-acl-associating-aws-resource.html).

!!! note "Policy scope"
    Automatic coverage applies only to resources within a central policy's scope.
    API Gateway coverage applies to REST API stages; AWS WAF does not support API Gateway HTTP APIs.
    Contact the Public Cloud team if you need help confirming coverage for your resource.

## Security requirements

Both customization options must preserve the central baseline:

- Include all five baseline managed rule groups, including the PHP rule group, regardless of your application's technology.
- Keep baseline rules ahead of application rules so application allow rules cannot bypass them.
- Preserve the baseline's rule actions and settings. Review any required exceptions with the Public Cloud team.
- Test your application rules and verify the associated web ACL before you rely on WAF protection.

## Baseline managed rule groups

The current baseline contains five **AWS managed rule groups**, each containing multiple rules. All use `AWS` as the vendor.

The central policy places these groups in its **first rule groups**, in this order:

| Managed rule group | Purpose |
| --- | --- |
| `AWSManagedRulesAmazonIpReputationList` | Identifies source IP addresses associated with malicious activity, using Amazon threat intelligence. |
| `AWSManagedRulesCommonRuleSet` | Protects against common web application threats, including cross-site scripting and other common exploits. |
| `AWSManagedRulesKnownBadInputsRuleSet` | Detects known malicious request patterns and inputs. |
| `AWSManagedRulesSQLiRuleSet` | Detects SQL injection attack patterns. |
| `AWSManagedRulesPHPRuleSet` | Detects attempts to exploit vulnerabilities in PHP applications. |

The first groups correspond to `pre_process_managed_rule_groups` in the central configuration.
The **last rule groups** list, `post_process_managed_rule_groups`, is currently empty (`[]`).

AWS maintains the rules inside these groups. The Public Cloud team manages which groups and settings form the central baseline.

For rule details, see the AWS references for
[IP reputation groups](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-ip-rep.html),
[baseline groups](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-baseline.html) and
[use-case specific groups](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-use-case.html).

## Option 1: Manage your own web ACL

Choose this option when your team wants to manage its own web ACL and application rules.
Your web ACL must include the central baseline as well as any rules your application needs.

1. Review the current central baseline, including rule actions and settings.
2. Create or update your web ACL with all five baseline managed rule groups, in the baseline order.
3. Add your application rules after the baseline groups.
4. Test the configuration, then associate the web ACL with your resource.
5. Verify the association and review rule metrics for unexpected matches.

Your team owns this web ACL's configuration and must keep it aligned with changes to the central baseline.
Adding the same managed group names alone does not ensure equivalent protection. Rule actions, overrides and other settings also matter.

The central web ACL does not provide an additional layer on top of your web ACL.
Your resource uses the single web ACL you associate with it.

## Option 2: Add rules to the centrally managed web ACL

Choose this option when you want the Public Cloud team to manage the baseline within your web ACL.
Use the web ACL that Firewall Manager manages in your workload account.
Your team adds application rules in the middle, between the central first and last rule groups.

| Evaluation order | Owner | Current contents |
| --- | --- | --- |
| First rule groups | Public Cloud team | The five baseline AWS managed rule groups |
| Middle rules and rule groups | Your team | Optional application rules, such as rate limits or path restrictions |
| Last rule groups | Public Cloud team | Empty |

1. Open AWS WAF in your workload account and select the appropriate Region or scope.
2. Find the centrally managed web ACL associated with your resource.
3. Review its first and last rule groups to understand the baseline.
4. Add your application rules between those groups. Keep the central groups under Public Cloud team management.
5. Test your rules and verify that the baseline groups and resource association remain in place.

Firewall Manager retains control of the first and last rule groups.
The empty last group list leaves room for future central rules; keep your application rules in the middle.

For details, see [Firewall Manager rule group management](https://docs.aws.amazon.com/waf/latest/developerguide/waf-policies-rule-groups.html).

!!! note "Rule evaluation can stop early"
    AWS WAF evaluates rules in priority order. An `Allow` or `Block` action stops evaluation of later rules.
    A middle rule cannot allow a request that a first rule already blocks.

    If central last groups contain rules in future, a middle `Allow` rule can skip them.
    Review application allow rules with this behaviour in mind.
    See [how AWS WAF handles rule actions](https://docs.aws.amazon.com/waf/latest/developerguide/web-acl-rule-actions.html).

### Manage application rules with Terraform

You can use Terraform to read the existing web ACL and manage your application rules within it.
The [`aws_wafv2_web_acl` data source](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/wafv2_web_acl)
returns identifying details, including the ARN. It does not return the full rule configuration or update the web ACL.

Use that ARN with an
[`aws_wafv2_web_acl_rule` resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/wafv2_web_acl_rule)
to manage an application rule. Firewall Manager continues to manage the central first and last groups.
Your team manages the application rule through Terraform state.

The following example adds a rate rule in `Count` mode to an existing centrally managed web ACL.
Use credentials for the workload account that owns the web ACL.
Set `web_acl_name` to its existing name and choose an unused priority among your application rules.

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.73"
    }
  }
}

variable "web_acl_name" {
  description = "Name of the existing centrally managed web ACL."
  type        = string
}

variable "waf_scope" {
  description = "Use REGIONAL for ALB or API Gateway, or CLOUDFRONT for CloudFront."
  type        = string
  default     = "REGIONAL"

  validation {
    condition     = contains(["REGIONAL", "CLOUDFRONT"], var.waf_scope)
    error_message = "Choose REGIONAL or CLOUDFRONT."
  }
}

provider "aws" {
  alias  = "waf"
  region = var.waf_scope == "CLOUDFRONT" ? "us-east-1" : "ca-central-1"
}

data "aws_wafv2_web_acl" "baseline" {
  provider = aws.waf
  name     = var.web_acl_name
  scope    = var.waf_scope
}

resource "aws_wafv2_web_acl_rule" "application_rate" {
  provider    = aws.waf
  web_acl_arn = data.aws_wafv2_web_acl.baseline.arn
  name        = "application-rate"
  priority    = 100

  action {
    count {}
  }

  statement {
    rate_based_statement {
      aggregate_key_type = "IP"
      limit              = 2000
    }
  }

  visibility_config {
    cloudwatch_metrics_enabled = true
    metric_name                = "application-rate"
    sampled_requests_enabled   = true
  }
}
```

For CloudFront, set `waf_scope = "CLOUDFRONT"`. The aliased provider then uses `us-east-1` for WAF operations.
The example's priority and rate threshold are starting values. Choose values that suit your application and existing rules.

Keep the existing ACL as a data source; manage only your additions as rule resources.
You do not need to import the centrally managed ACL as an `aws_wafv2_web_acl` resource for this approach.
Review `terraform plan` to confirm that it changes only your application rules.
Apply changes through one Terraform state for those rules, then verify the baseline and resource association.

To manage a custom rule group, use
[`aws_wafv2_web_acl_rule_group_association`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/wafv2_web_acl_rule_group_association)
with the existing web ACL ARN.
This adds a rule group reference within the ACL; it does not change the protected resource's web ACL association.

## Verify your WAF configuration

After deployment or a rule change:

1. Confirm that the intended web ACL has an association with your resource.
2. Confirm that all five baseline managed rule groups remain present with the required settings.
3. Check that application rules follow the baseline and stay between any central first and last groups.
4. Test normal application requests and review matches through AWS WAF metrics, sampled requests or configured logs.

Test new application rules with the `Count` action before you enable blocking where appropriate.
Keep the central baseline settings in place while you test your additions.

If a baseline rule blocks valid traffic, contact the Public Cloud team to review the issue.
Include your Project Set licence plate, account, Region, resource ARN, web ACL name and relevant rule matches.
Use the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3) for support.

## Related pages

- [AWS security and compliance guardrails](../get-started-with-lza/guardrails.md)
- [Networking in AWS LZA](networking.md)
- [Publish an internal Application Load Balancer](alb-service.md)
- [Infrastructure as code and CI/CD in AWS LZA](../best-practices/iac-and-ci-cd.md)
- [Public cloud support](../../../welcome/support.md)
- [Using AWS WAF policies with Firewall Manager](https://docs.aws.amazon.com/waf/latest/developerguide/waf-policies.html)
- [Associate a web ACL with an AWS resource](https://docs.aws.amazon.com/waf/latest/developerguide/web-acl-associating-aws-resource.html)
- [Azure Front Door WAF guidance](../../../azure/azure-services/front-door.md#security-requirements)
