# Amazon SES: Prefer API + IAM roles; use SMTP only for legacy systems

This document describes how to send email with Amazon Simple Email Service (SES) in our AWS Landing Zone. It also explains why **SMTP credentials are a fallback option**, not the default.

Amazon SES supports two ways to send email:

- **Amazon SES API (recommended)**  
- **SES SMTP interface (legacy / compatibility option)**

## Recommendation

### Preferred: Use the SES API with IAM roles (no long-lived secrets)

When possible, applications should send email by calling the **SES API** through the (AWS SDK/CLI). This allows workloads to authenticate using **IAM roles** , including assumed roles.

!!! note "Important nuance"

    Role assumption works only with the **SES API**. The SMTP interface does **not** support role-based authentication.

### Use SMTP only for legacy systems that cannot use the SES API

Some legacy software only supports SMTP. In those cases, SES SMTP is acceptable, with the following constraints:

- SES SMTP credentials are **different** from AWS access keys.

- You **must not** derive SMTP credentials from **temporary (STS) credentials**—the SES SMTP interface doesn’t support SMTP credentials generated from temporary security credentials.

**Therefore:** SMTP requires a long-lived secret , derived from an IAM user access key. Because of this, treat SMTP as a compatibility option, not a default integration method.

## Landing Zone constraint: why“Create SMTP Credentials” often fails in the SES console

The SES console includes a **create SMTP credentials** option that relies on IAM user management behind the scenes. AWS documents that this process requires IAM permissions such as:

- `iam:ListUsers`
- `iam:CreateUser`
- `iam:CreateAccessKey`
- `iam:PutUserPolicy`

In our AWS Landing Zone, Service Control Policies (SCPs) restrict IAM user creation. As a result, console workflows that attempt to create IAM users, including the SES Create SMTP Credentials flow, will fail.

## Supported SMTP approach in this Landing Zone (when SMTP is unavoidable)

If a system must use SMTP, follow this process:

1. Use an **existing IAM user**, or request a new IAM user through our [IAM automation/service](iam-user-service.md).
2. Grant only the minimum required **SES sending permissions** for the intended use.
3. **Generate the SMTP password** from the IAM user’s secret access key using AWS’s documented process:
   - **SMTP username:** IAM Access Key ID
   - SMTP password = **derived** value (not the AWS secret access key itself)  [Obtaining Amazon SES SMTP credentials](https://docs.aws.amazon.com/ses/latest/dg/smtp-credentials.html)
