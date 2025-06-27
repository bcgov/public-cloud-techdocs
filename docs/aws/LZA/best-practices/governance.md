# Governance

Last updated: **{{ git_revision_date_localized }}**

This document describes how to manage and govern your AWS Landing Zone Accelerator (LZA) environment. While the Public Cloud team provides initial guardrails and standards, **each ministry team is responsible** for managing their own resources and ensuring compliance with established standards.

!!! info "Shared Responsibility"
    Governance in LZA follows a shared responsibility model - the platform provides the foundation and guardrails, while teams manage their applications and ensure ongoing compliance.

## Security posture management

### AWS Security Hub

Navigate to [AWS Security Hub](https://ca-central-1.console.aws.amazon.com/securityhub/home?region=ca-central-1#/summary) in the AWS Console to view your security posture. Security Hub provides:

- **Centralized security findings** from multiple AWS security services
- **Security standards compliance** including AWS Foundational Security Standard and CIS benchmarks
- **Actionable insights** to improve your security posture
- **Integration** with GuardDuty, Inspector, and other security services

!!! tip "Security Hub Benefits"
    Security Hub aggregates findings from multiple security services, making it easier to prioritize and address security issues across your AWS environment.

### AWS GuardDuty

[AWS GuardDuty](https://ca-central-1.console.aws.amazon.com/guardduty/home?region=ca-central-1#/findings) continuously monitors for malicious activity and unauthorized behavior:

- **Threat detection** using machine learning and threat intelligence
- **Behavioral analysis** of API calls and network traffic
- **Automated findings** with severity levels and remediation guidance
- **Integration** with Security Hub for centralized management

### AWS Config

[AWS Config](https://ca-central-1.console.aws.amazon.com/config/home?region=ca-central-1#/dashboard) tracks resource configurations and compliance:

- **Configuration tracking** for AWS resources
- **Compliance monitoring** against AWS Config rules
- **Change history** and configuration timelines
- **Automated remediation** for certain compliance issues

## Compliance monitoring

### AWS Organizations Policies

Your AWS account is governed by Service Control Policies (SCPs) and other organizational policies:

- **Service Control Policies** prevent actions that violate security or compliance requirements

!!! warning "Policy Enforcement"
    Organizational policies are enforced at the OU level and cannot be overridden. These policies implement the [guardrails](../get-started-with-lza/guardrails.md) that ensure platform security and compliance.

### Compliance Reporting

Monitor your compliance status through:

- **Security Hub compliance scores** for security standards
- **Config compliance dashboard** for configuration rules
- **AWS Well-Architected reviews** for operational excellence
- **Custom compliance reports** using AWS APIs and tools

## Cost governance

### AWS Cost Explorer

Navigate to [AWS Cost Explorer](https://ca-central-1.console.aws.amazon.com/cost-management/home?region=ca-central-1#/custom) to analyze your spending:

- **Cost visualization** with multiple dimensions and filters
- **Trend analysis** to identify spending patterns
- **Resource-level costs** for detailed cost attribution
- **Forecasting** to predict future costs

!!! example "Cost Analysis Best Practices"

    - **Filter by service** to identify your largest cost drivers
    - **Group by resource tags** to organize cost reporting
    - **Set up cost alerts** to monitor spending against budgets
    - **Review monthly** to identify optimization opportunities

### AWS Budgets

[AWS Budgets](https://ca-central-1.console.aws.amazon.com/billing/home#/budgets) helps you monitor and control costs:

- **Cost budgets** to track spending against planned amounts
- **Usage budgets** to monitor resource consumption
- **Reservation budgets** for Reserved Instance utilization
- **Automated alerts** when thresholds are exceeded

For detailed cost management guidance, see [AWS Cost Management](../understanding-your-bill/aws-cost-management.md).

## Resource management

### Tagging strategy

Implement consistent resource tagging for:

- **Resource organization** - Organize and group related resources by project, team, or environment
- **Automation** - Enable automated workflows based on tags
- **Compliance** - Meet organizational tagging requirements

!!! tip "Tagging Best Practices"
    Establish a tagging strategy early and enforce it consistently. Common tags include Environment, Project, Owner, and Purpose.

### Resource lifecycle management

Manage resources throughout their lifecycle:

- **Provisioning** - Use Infrastructure as Code for consistent deployments
- **Monitoring** - Track resource utilization and performance
- **Optimization** - Right-size resources based on actual usage
- **Decommissioning** - Remove unused resources to control costs

### Backup and disaster recovery

Ensure business continuity through:

- **Automated backups** for critical data and systems
- **Cross-region replication** where permitted by guardrails
- **Recovery testing** to validate backup and restore procedures
- **Documentation** of recovery processes and contact information

## Access governance

### Identity and access management

Access to AWS resources is managed through:

- **Entra ID integration** for user authentication
- **Security groups** with standardized naming conventions
- **Role-based access control** with least privilege principles
- **Regular access reviews** to ensure appropriate permissions

See [User Management](../design-build-deploy/user-management.md) for detailed access control information.

### Privileged access management

For administrative access:

- **Multi-factor authentication** required for all access
- **Session recording** for audit and compliance
- **Regular review** of privileged access assignments

## Operational governance

### Change management

Implement proper change management:

- **Infrastructure as Code** for all resource changes
- **Code reviews** for infrastructure modifications
- **Testing procedures** in non-production environments
- **Rollback plans** for failed deployments

### Monitoring and alerting

Establish comprehensive monitoring:

- **CloudWatch metrics** for resource performance
- **Custom dashboards** for business-critical metrics
- **Automated alerts** for threshold breaches
- **Log aggregation** for troubleshooting and audit

### Incident management

Prepare for incident response:

- **Incident response procedures** documented and tested
- **Contact information** for escalation paths
- **Communication plans** for stakeholder updates
- **Post-incident reviews** for continuous improvement

## Continuous improvement

### Regular reviews

Conduct periodic reviews of:

- **Security posture** using Security Hub and other tools
- **Cost optimization** opportunities through Cost Explorer
- **Resource utilization** to identify waste or over-provisioning
- **Compliance status** against organizational requirements

### Best practices adoption

Stay current with:

- **AWS Well-Architected Framework** principles
- **Security best practices** from AWS and industry sources
- **Cost optimization** techniques and new service features
- **Platform updates** and new LZA capabilities

## Getting support

For governance-related questions or issues:

1. **Review documentation** - Check our comprehensive guides and best practices
2. **Use AWS support** - Leverage Enterprise Support for technical guidance
3. **Contact the platform team** - Submit requests through the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3)
4. **Join community discussions** - Participate in user groups and knowledge sharing

## Related documentation

- [Guardrails](../get-started-with-lza/guardrails.md) - Security and compliance controls
- [Requirements](../design-build-deploy/requirements.md) - Platform limitations and constraints
- [User Management](../design-build-deploy/user-management.md) - Access control and permissions
- [AWS Cost Management](../understanding-your-bill/aws-cost-management.md) - Detailed cost guidance
- [Enterprise Support](../support/enterprise-support.md) - Getting help from AWS
