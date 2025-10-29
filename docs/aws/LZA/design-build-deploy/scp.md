# Service Control Policies (SCPs) in our LZA (quick note)

Last updated: **{{ git_revision_date_localized }}**

## What are SCPs?

- Org-level guardrails in AWS Organizations
- **Deny-only:** they set the maximum permissions identities can ever use within an OU/account
- Attach at the **root/OU/account** level—applies to everything beneath
- They **don’t** grant permissions; identity policies are still required

## LZA baseline SCPs (CCCS-Medium)

The LZA (CCCS-Medium variant) ships a curated set of SCPs that form our **baseline** security guardrails.

- **Source of truth:** [CCCS-Medium Service Control Policies](https://github.com/aws-samples/landing-zone-accelerator-on-aws-for-cccs-medium/tree/main/config/service-control-policies)

## Our custom SCP: `bcgov-lza-scp` (what & why)

**What:** A small, targeted SCP attached to workload OU(s) to protect **platform-managed** assets and automations the Public Cloud team deploys during account vending.

**Why:** To preserve the **availability and integrity** of platform services (e.g., IAM User Service, ALB Automation). Without this custom guardrail, accidental or unauthorized changes could disrupt tenant workloads.
