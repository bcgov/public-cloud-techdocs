# Back up resources in AWS LZA

Last updated: **{{ git_revision_date_localized }}**

AWS Landing Zone Accelerator (LZA) provides a centrally managed AWS Backup feature. It supports AWS Backup eligible resources in workload accounts.

Workload teams enrol resources by applying approved backup tags. The Landing Zone creates and manages backup plans, the backup vault, and schedules.

The Landing Zone also manages the service role and organization-level backup policies.

This page explains how to use tag-based backup enrolment in AWS LZA. It does not replace the full [AWS Backup documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html).

---

!!! info "AWS LZA region"
    AWS LZA uses Canada (Central), `ca-central-1`, for workload resources.

## Benefits

The central backup feature provides:

- Standardized backup schedules across workload accounts
- Tag-based enrolment for supported AWS resources
- Centrally managed backup vaults and service roles
- Governed backup policy deployment through AWS Organizations
- Simplified onboarding for workload teams
- Consistent recovery point creation for tagged resources

Backups help protect data, but they do not replace high availability, disaster recovery design, replication, or application-level data protection.

## How the feature works

The Landing Zone manages AWS Backup policies through AWS Organizations. These policies create backup plans and resource selections in workload accounts.

Workload teams do not need to create backup plans, backup vaults, schedules, or automation. Instead, apply the correct tag to each resource that needs backup coverage.

The Landing Zone uses these centrally managed resources in workload accounts:

| Resource | Value |
| --- | --- |
| Backup vault | `AWSAccelerator-BackupVault` |
| AWS Backup service role | `AWSAccelerator-Backup-Role` |

The central AWS Backup service role uses AWS managed policies for backup and restore actions. These include:

- `AWSBackupServiceRolePolicyForBackup`
- `AWSBackupServiceRolePolicyForRestores`
- `AWSBackupServiceRolePolicyForS3Backup`
- `AWSBackupServiceRolePolicyForS3Restore`

Workload teams do not manage this role or its policies.

The Landing Zone creates these centrally managed backup schedules:

| Schedule | Tag value | Resource tag | When it runs |
| --- | --- | --- | --- |
| Continuous | `Continuous` | `S3BackupPlan` | Continuous protection for supported S3 buckets |
| Hourly | `Hourly` | `BackupPlan` or `S3BackupPlan` | Hourly |
| Daily | `Daily` | `BackupPlan` or `S3BackupPlan` | Daily at 05:00 UTC |
| Weekly | `Weekly` | `BackupPlan` or `S3BackupPlan` | Weekly on Monday at 05:00 UTC |
| Monthly | `Monthly` | `BackupPlan` or `S3BackupPlan` | Monthly on the first day at 05:00 UTC |

The Landing Zone controls the exact cron expressions, retention periods, and lifecycle settings.

For AWS Backup to protect a resource, all of these conditions must be true:

1. AWS Backup supports the resource type.
2. The resource has the correct backup tag.
3. The tag key and value exactly match an available backup plan.
4. Service-specific prerequisites are met.

!!! note "Central service enablement"
    The Landing Zone team manages AWS Backup service enablement. Workload teams do not need to change AWS Organizations backup opt-in settings.

    A workload account setting does not replace the organization-level configuration when organization backup policies apply.

## Supported backup tags

Tag keys and values are case-sensitive. They must match exactly.

### Tags for most resources

Use the `BackupPlan` tag for most AWS Backup resource types.

| Tag key | Supported values | Example |
| --- | --- | --- |
| `BackupPlan` | `Hourly`, `Daily`, `Weekly`, `Monthly` | `BackupPlan=Daily` |

Valid examples:

```text
BackupPlan=Hourly
BackupPlan=Daily
BackupPlan=Weekly
BackupPlan=Monthly
```

Invalid examples:

```text
backupplan=Hourly
BackupPlan=hourly
BackupPlan= Hourly
```

### Tags for Amazon S3

Use the `S3BackupPlan` tag for Amazon S3 buckets.

| Tag key | Supported values | Example |
| --- | --- | --- |
| `S3BackupPlan` | `Continuous`, `Hourly`, `Daily`, `Weekly`, `Monthly` | `S3BackupPlan=Daily` |

Valid examples:

```text
S3BackupPlan=Continuous
S3BackupPlan=Hourly
S3BackupPlan=Daily
S3BackupPlan=Weekly
S3BackupPlan=Monthly
```

S3 backup options:

| S3 option | Tag values | Behaviour |
| --- | --- | --- |
| Periodic backup | `Hourly`, `Daily`, `Weekly`, `Monthly` | Creates scheduled snapshot-style recovery points for the selected plan. |
| Continuous backup | `Continuous` | Tracks changes and supports point-in-time recovery behaviour through continuous protection. |

!!! warning "Use one S3 backup value"
    Choose either continuous S3 backup or one periodic S3 schedule. A bucket has one `S3BackupPlan` value, such as `Continuous` or `Daily`.

## AWS Backup service coverage

The Landing Zone enables AWS Backup centrally in the management account for all resource types that AWS Backup supports.

To confirm whether AWS Backup supports a service or resource type, review the [AWS Backup documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html).

## Responsibilities

### Landing Zone team

The Landing Zone team manages:

- AWS Organizations backup policies
- Central backup plan definitions
- Backup schedules
- Backup lifecycle and retention configuration
- Backup vault deployment
- AWS Backup service roles
- Organization-level AWS Backup service opt-in
- Standard backup tag policy definitions

### Workload teams

Workload teams manage:

- Identifying which resources need backup
- Applying the correct backup tag
- Choosing the appropriate schedule
- Understanding application-specific consistency requirements
- Verifying backup coverage after tagging
- Running restore testing for their application
- Maintaining service-specific prerequisites, such as S3 Versioning
- Removing or changing tags when backup needs change
- Understanding that backup does not replace disaster recovery design

Tagging a resource enrols it into a centrally managed plan. Workload owners still own their recovery requirements and restore validation.

## Service-specific prerequisites

### General

Before you apply a backup tag, confirm:

- AWS Backup supports the resource type.
- The workload meets any service-specific AWS Backup requirements.

### Amazon S3

Amazon S3 has specific requirements and limitations.

Before you tag an S3 bucket, confirm:

- You enable S3 Versioning.
- The bucket uses the `S3BackupPlan` tag.
- Continuous S3 backup relies on EventBridge integration.

For the complete list of S3 backup requirements and limitations, review the [AWS Backup documentation for Amazon S3](https://docs.aws.amazon.com/aws-backup/latest/devguide/s3-backups.html).

## Applying backup tags

Apply backup tags through your normal infrastructure as code process where possible. This keeps backup coverage auditable and repeatable.

### EBS volume example

Apply this tag to an EBS volume:

```text
BackupPlan=Hourly
```

Expected behaviour: the hourly resource selection discovers the EBS volume. AWS Backup creates scheduled recovery points in the centrally managed backup vault.

### DynamoDB example

Apply this tag to a DynamoDB table:

```text
BackupPlan=Daily
```

Expected behaviour: AWS Backup enrols the DynamoDB table in the daily centrally managed backup plan.

### S3 periodic example

Enable S3 Versioning and apply this tag to an S3 bucket:

```text
S3BackupPlan=Daily
```

Expected behaviour: AWS Backup creates periodic scheduled S3 recovery points by using the daily plan.

### S3 continuous example

Enable S3 Versioning and apply this tag to an S3 bucket:

```text
S3BackupPlan=Continuous
```

Expected behaviour: AWS Backup enrols the bucket in continuous protection and maintains point-in-time recovery coverage.

## Verifying backup coverage

Tag propagation and resource discovery may take time. AWS Backup generally evaluates the resource during the next eligible backup-plan execution. The timing depends on the selected plan.

Wait for the next scheduled run, then verify the backup job or recovery point.

### Console verification

Use the AWS Backup console for the simplest verification path:

1. Open the AWS Backup console.
2. Open **Protected resources**.
3. Search for the resource ARN or resource name.
4. Open **Backup jobs** to review job status.
5. Open **Backup vaults**.
6. Select `AWSAccelerator-BackupVault`.
7. Confirm that recovery points exist for the resource.

## Changing or removing backup protection

When you change the tag value, AWS Backup moves the resource to a different centrally managed schedule. The change applies after AWS Backup re-evaluates the resource.

When you remove the tag, AWS Backup stops future backups under that tag-based plan. The change applies after AWS Backup recognizes the new tag state.

AWS Backup does not always delete existing recovery points when you remove a tag. Configured retention and lifecycle policies still govern those recovery points.

## Limitations and considerations

Review these points before relying on central backups:

- Backup coverage does not start immediately after tagging.
- The next backup depends on tag discovery and the selected schedule.
- The Landing Zone configuration controls exact schedules, retention, and lifecycle settings.
- Application consistency remains a workload-team responsibility.
- Backups do not replace disaster recovery, replication, high availability, or application-level protection.
- S3 continuous backup does not produce hourly backup jobs.
- S3 backup does not restore all bucket-level configuration.

The Landing Zone team manages organization-level AWS Backup service opt-in. Workload teams do not need to manage this setting.

## Troubleshooting

| Symptom | Checks |
| --- | --- |
| No backup job appears | Confirm the tag key, tag value, capitalization, AWS Backup service support, service prerequisites, and timing of the next plan execution. |
| S3 backup does not appear | Confirm the bucket uses `S3BackupPlan`. Confirm that you enabled S3 Versioning. Confirm that you used the correct value. Check that you are not confusing continuous and periodic behaviour. Contact the Landing Zone team if these checks pass. |
| Resource appears as protected, but no recent scheduled backup exists | An older manual backup or previous recovery point can make a resource appear in **Protected resources**. Check the latest backup job and recovery-point creation time. |

## References

- [AWS Backup documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)
- [AWS Backup documentation for Amazon S3](https://docs.aws.amazon.com/aws-backup/latest/devguide/s3-backups.html)
