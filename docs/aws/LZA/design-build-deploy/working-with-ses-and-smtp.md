# Amazon SES: Prefer API + IAM roles; use SMTP only for legacy systems

This document explains our recommended approach for sending email with Amazon Simple Email Service (SES) in our AWS Landing Zone, and why **SMTP credentials should be treated as a fallback**.

Amazon SES supports two primary ways to send email:

- **Amazon SES API (recommended)**  
- **SES SMTP interface (legacy / compatibility option)**

## Recommendation

### Preferred: Use the SES API with IAM roles (no long-lived secrets)

Wherever possible, integrate applications with SES using the **SES API** (AWS SDK/CLI). This allows workloads to authenticate using **IAM roles** (including assumed roles), avoiding long-lived user credentials entirely.

!!! note "Important nuance"

    Use of role assumption applies to the **SES API**, not SMTP. The SMTP interface does **not** use role-based authentication.

### Use SMTP only for legacy systems that cannot use the SES API

Some legacy software only supports SMTP. In those cases, SES SMTP is acceptable, but note:

- SES SMTP credentials are **not the same** as AWS access keys.

- You **must not** derive SMTP credentials from **temporary (STS) credentials**—the SES SMTP interface doesn’t support SMTP credentials generated from temporary security credentials.

**Therefore:** SMTP requires a long-lived secret (derived from an IAM user access key), so we treat it as a compatibility option rather than the default.

## Landing Zone constraint: why the SES console “Create SMTP Credentials” often fails

The SES console flow for creating SMTP credentials relies on IAM user management behind the scenes. AWS documents that creating SMTP credentials via the console requires IAM permissions such as:

- `iam:ListUsers`
- `iam:CreateUser`
- `iam:CreateAccessKey`
- `iam:PutUserPolicy`

In our Landing Zone, IAM user creation is restricted by SCP guardrails, so workflows that attempt to create IAM users directly (including the SES console “Create SMTP Credentials” path) will be denied.

## Our supported SMTP approach in this Landing Zone (when SMTP is unavoidable)

When a system must use SMTP:

1. Use an **existing IAM user** (or a new IAM user provisioned via our [IAM automation/service](iam-user-service.md)).
2. Ensure it has the minimum required **SES sending permissions** for the intended use.
3. **Derive the SMTP password** from the IAM user’s secret access key using AWS’s documented method:
   - SMTP username = IAM **Access Key ID**
   - SMTP password = **derived** value (not the AWS secret access key itself)  [Obtaining Amazon SES SMTP credentials](https://docs.aws.amazon.com/ses/latest/dg/smtp-credentials.html)
