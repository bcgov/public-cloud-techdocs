# Azure Communication Services

Last updated: **{{ git_revision_date_localized }}**

Use this page to plan a replacement for Azure Communication Services (ACS) workloads.

---

!!! warning "Plan your migration"
    Microsoft retires standalone ACS services on September 30, 2028.

    Start your migration before that date to avoid service disruption.

## What's changing

Microsoft announced the retirement of standalone ACS in September 2026. It limits onboarding and expansion for retiring services starting October 23, 2026.

ACS resources created before October 23, 2026, can support existing workloads during the transition. Microsoft may add restrictions during this period.

Microsoft removes the following ACS capabilities on September 30, 2028:

- ACS Email
- ACS SMS
- ACS Chat and ACS Rooms
- ACS Advanced Messaging for WhatsApp
- ACS Number Management and Direct Routing
- ACS Job Router
- ACS UI Library SDKs

Other ACS calling services and SDKs have breaking changes instead of retirement. To continue using these services after September 30, 2028, migrate to a supported Teams-aligned solution and update to the new major-version SDKs by that date.

Review the full [retirement and breaking changes guide](https://learn.microsoft.com/en-us/azure/communication-services/acs-retirement-and-breaking-changes-guide). Microsoft lists each affected service and its supported migration path.

## Choose an alternative

### Amazon SES for email

Use [Amazon Simple Email Service (SES)](https://docs.aws.amazon.com/ses/latest/dg/Welcome.html) as the primary alternative for ACS Email.

Amazon SES supports transactional and bulk email. Applications can send email through the SES API or SMTP interface.

Use the SES API with AWS IAM roles when possible. Use SMTP only for legacy systems that require it.

Read [Amazon SES: Prefer API and IAM roles](../../aws/LZA/design-build-deploy/working-with-ses-and-smtp.md) for Landing Zone guidance. The page explains the approved SMTP approach and IAM restrictions.

### Microsoft email services

Consider Microsoft 365 High Volume Email for automated messages to internal recipients. HVE accounts do not require Microsoft 365 licenses; each account must be configured with a billing policy linked to an Azure subscription for pay-as-you-go billing.

Read [Manage High Volume Email for Microsoft 365](https://learn.microsoft.com/en-us/exchange/mail-flow-best-practices/high-volume-mails-m365).

Exchange Online supports other application email patterns. Review [how to send email from an application by using Microsoft 365](https://learn.microsoft.com/en-us/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365).

### Other ACS capabilities

Choose a replacement that matches the ACS capability:

- Use a supported Microsoft Teams service for calling and meeting scenarios.
- Use Microsoft Graph APIs for eligible chat scenarios.
- Use a communications provider from Microsoft's migration guide for SMS, WhatsApp, or telephony.

## Plan the email migration

1. List each ACS feature, sender domain, endpoint, and SDK that your application uses.
2. Choose Amazon SES, Microsoft 365, Exchange Online, or another approved provider.
3. Verify your domain. Configure SPF, DKIM, and DMARC before you change production traffic.
4. Request Amazon SES production access before you send to unverified recipients.
5. Test delivery, templates, attachments, bounce handling, and suppression lists.
6. Move a small workload first. Monitor delivery and complaint rates before you complete the migration.
7. Remove the ACS integration before September 30, 2028.

Follow [Migrating to Amazon SES from another email-sending solution](https://docs.aws.amazon.com/ses/latest/dg/send-email-getting-started-migrate.html) for the provider-specific steps.

## Related pages

- [ACS retirement and breaking changes guide](https://learn.microsoft.com/en-us/azure/communication-services/acs-retirement-and-breaking-changes-guide)
- [Overview of Azure Communication Services email](https://learn.microsoft.com/en-us/azure/communication-services/concepts/email/email-overview)
- [Amazon SES: Prefer API and IAM roles](../../aws/LZA/design-build-deploy/working-with-ses-and-smtp.md)
- [Support](../../welcome/support.md)
