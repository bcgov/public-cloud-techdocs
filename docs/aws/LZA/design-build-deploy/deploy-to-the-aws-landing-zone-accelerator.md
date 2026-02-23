# Deploy to the AWS Landing Zone Accelerator

Last updated: **{{ git_revision_date_localized }}**

This guide covers the process of deploying applications and infrastructure to the AWS Landing Zone Accelerator (LZA) platform.

## Prerequisites

Before you begin deploying to LZA, ensure you have:

1. **Account access**: Access to your project set's AWS accounts
2. **Appropriate permissions**: The correct role assignments for your deployment needs
3. **Understanding of your architecture**: Knowledge of which account(s) your resources should be deployed to

## Accessing your AWS accounts

To deploy applications, you'll need to access your AWS accounts through the AWS SSO Portal:

**🔗 [Access AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts)**

### Deployment account selection

Choose the appropriate account for your deployment:

- **Development Account (`{project}-dev`)**: For development work and initial testing
- **Test Account (`{project}-test`)**: For integration testing and staging
- **Production Account (`{project}-prod`)**: For production deployments (requires Admin role)
- **Tools Account (`{project}-tools`)**: For CI/CD pipelines, shared tools, and cross-account resources

### Required permissions for deployment

- **Developers**: Can deploy to dev, test, and tools accounts
- **Admins**: Can deploy to all accounts including production
- **Viewers**: Read-only access (cannot deploy)

For detailed information about roles and permissions, see our [user management guide](user-management.md).

## Deployment methods

### 1. AWS Management Console

1. Log into the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts)
2. Select your target account
3. Choose your role (Developer or Admin)
4. Use the AWS Management Console to create and manage resources

### 2. AWS CLI

Configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) to work with your LZA accounts:

```bash
# Configure AWS CLI with SSO
aws configure sso

# Or use temporary credentials from the SSO portal
# Copy the credentials from the "Command line or programmatic access" option
```

For detailed SSO configuration, see the [AWS CLI SSO documentation](https://docs.aws.amazon.com/cli/latest/userguide/sso-configure-profile-token.html).

### 3. Infrastructure as Code (IaC)

We recommend using Infrastructure as Code for consistent, repeatable deployments:

- **[AWS CloudFormation](https://docs.aws.amazon.com/cloudformation/)**: AWS native IaC service
- **[AWS CDK](https://docs.aws.amazon.com/cdk/)**: Code-based infrastructure definition
- **[Terraform](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)**: Multi-cloud infrastructure tool with AWS Provider
- **[Pulumi](https://www.pulumi.com/docs/clouds/aws/)**: Modern infrastructure as code platform

### 4. CI/CD pipelines

Deploy your CI/CD pipeline resources to the **Tools account** for centralized management:

- Use [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/), [CodeBuild](https://docs.aws.amazon.com/codebuild/), and [CodeDeploy](https://docs.aws.amazon.com/codedeploy/)
- Or integrate with external CI/CD tools like [GitHub Actions](https://docs.github.com/en/actions), Jenkins, or GitLab CI
- Ensure your pipeline has appropriate cross-account permissions for deployments

For complete CI/CD guidance, see the [AWS Developer Tools documentation](https://docs.aws.amazon.com/dtconsole/latest/userguide/).

## Deployment best practices

### Security considerations

1. **Use least privilege**: Only request the minimum permissions needed for your deployment
2. **Environment separation**: Deploy to appropriate accounts based on your development lifecycle
3. **Secrets management**: Use [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/) or [Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) for sensitive data
4. **Network security**: Follow networking guidelines and use [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/security-groups.html) appropriately

For comprehensive security guidance, see the [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/).

### Resource organization

1. **Tagging strategy**: Apply consistent [tags](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging.html) to all resources
2. **Resource naming**: Use clear, consistent naming conventions following [AWS naming best practices](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/naming-conventions.html)
3. **Resource groups**: Organize related resources using [AWS Resource Groups](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html)

### Monitoring and logging

1. **CloudWatch**: Use [AWS CloudWatch](https://docs.aws.amazon.com/cloudwatch/) for monitoring and alerting
2. **CloudTrail**: All API calls are automatically logged for audit purposes via [AWS CloudTrail](https://docs.aws.amazon.com/cloudtrail/)
3. **Cost monitoring**: Use [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) and [budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) to track spending

## Common deployment scenarios

### Web application deployment

1. **Frontend**: Deploy static assets to [Amazon S3](https://docs.aws.amazon.com/s3/) with [CloudFront](https://docs.aws.amazon.com/cloudfront/) distribution
2. **Backend**: Use [Elastic Container Service (ECS)](https://docs.aws.amazon.com/ecs/) or [Lambda](https://docs.aws.amazon.com/lambda/) for application logic
3. **Database**: Deploy [RDS instances](https://docs.aws.amazon.com/rds/) or use [DynamoDB](https://docs.aws.amazon.com/dynamodb/) for data storage
4. **Load balancing**: Use [Application Load Balancer (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/) or [API Gateway](https://docs.aws.amazon.com/apigateway/) for traffic distribution

### Containerized applications

1. **Container registry**: Use [Amazon ECR](https://docs.aws.amazon.com/ecr/) to store container images
2. **Orchestration**: Deploy using [Amazon ECS](https://docs.aws.amazon.com/ecs/) or [Amazon EKS](https://docs.aws.amazon.com/eks/)
3. **Service discovery**: Use [AWS Service Discovery](https://docs.aws.amazon.com/cloud-map/) for microservices
4. **Scaling**: Configure [auto-scaling](https://docs.aws.amazon.com/autoscaling/) based on demand

### Serverless applications

1. **Functions**: Deploy [AWS Lambda](https://docs.aws.amazon.com/lambda/) functions for event-driven computing
2. **API Gateway**: Create REST or GraphQL APIs using [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/)
3. **Event processing**: Use [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/) for event routing
4. **Data processing**: Use [AWS Step Functions](https://docs.aws.amazon.com/step-functions/) for workflow orchestration

For serverless architecture patterns, see the [AWS Serverless Application Lens](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html).

## Troubleshooting deployment issues

### Access problems

- Verify you're logged into the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts)
- Check that you have the appropriate role for your target account
- Allow up to 40 minutes for new permissions to propagate

### Permission errors

- Ensure your role has sufficient permissions for the resources you're creating
- Check if Azure Policy restrictions are blocking your actions
- Contact your Product Owner or Technical Lead if you need additional permissions

### Resource creation failures

- Review [CloudFormation stack events](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html) for detailed error messages
- Check [service quotas and limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in your account
- Verify that resource names don't conflict with existing resources

## Getting help

If you encounter issues during deployment:

- **Documentation**: Review our [best practices guides](../best-practices/be-mindful.md)
- **Support**: Contact the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3)
- **Tools**: Use [helpful tools](../tools/tools.md) for debugging and management

## Next steps

After successful deployment:

1. **Monitor your application**: Set up [CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) and [alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)
2. **Implement backup strategies**: Configure automated backups using [AWS Backup](https://docs.aws.amazon.com/aws-backup/) where appropriate
3. **Plan for scaling**: Design your architecture to handle increased load following [AWS Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/) best practices
4. **Cost optimization**: Regularly review and optimize your resource usage with [AWS Cost Optimization Hub](https://docs.aws.amazon.com/cost-management/latest/userguide/cost-optimization-hub.html)
5. **Security review**: Conduct regular security assessments using [AWS Security Hub](https://docs.aws.amazon.com/securityhub/) and [AWS Well-Architected Tool](https://docs.aws.amazon.com/wellarchitected/)

For more detailed guidance, explore our [best practices section](../best-practices/be-mindful.md) and [helpful tools](../tools/tools.md).
