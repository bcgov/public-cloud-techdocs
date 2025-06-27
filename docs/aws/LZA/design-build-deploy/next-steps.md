# Next steps

Last updated: **{{ git_revision_date_localized }}**

At this point, you should have a Project Set provisioned in AWS Landing Zone Accelerator (LZA). Now what?

!!! tip "AWS Training Resources"
    Please refer to the [AWS training resources](#reference-aws-training-resources) list at the end of this page for a curated list of additional training on AWS concepts and services related to these topics.

## Networking

Per the [AWS Landing Zone Accelerator Overview](../get-started-with-lza/aws-landing-zone-accelerator-overview.md#your-project-set-structure), each Project Set has its own VPC in each account to isolate resources and provide secure connectivity.

Since most projects will deploy common AWS resources that have network security guardrails, you will need to **create Security Groups** within the VPC to control traffic between your resources and potentially **request additional networking** if you encounter IP address limitations.

!!! quote "Security Groups vs Network ACLs"
    [Security Groups documentation](https://docs.aws.amazon.com/vpc/latest/userguide/security-groups.html), "_Security groups act as a virtual firewall for your instances to control inbound and outbound traffic._" They are stateful and provide instance-level security.

    LZA provides platform-managed Network ACLs and some security groups, but you can create additional security groups as needed for your specific application requirements.

!!! tip "Security Group creation"
    You can create new Security Groups through the AWS Management Console, AWS CLI, or Infrastructure as Code tools like Terraform. However, platform-created security groups with Accelerator tags cannot be modified.

The VPC structure in LZA includes pre-configured subnets:

- **Web-MainTgwAttach subnets** (2 × /27): For internet-facing resources and Transit Gateway connectivity
- **App subnets** (2 × /26): For application servers and containers  
- **Data subnets** (2 × /28): For databases and data storage
- **Management subnets** (2 × /28): For administrative and monitoring tools

!!! warning "IP Address Limitations"
    Each VPC has a /24 CIDR range providing only 256 IP addresses total. Plan your resource deployment carefully, and consider requesting a [secondary CIDR range](networking.md#extended-network-ip-exhaustion-solution) early if you anticipate high resource density.

## Working with AWS Services

AWS LZA provides a secure foundation for deploying various AWS services. When you deploy AWS resources, you should follow security best practices and use appropriate service configurations.

### Connecting to your resources

Because LZA implements security guardrails, many resources will be deployed in private subnets without direct internet access. You have several options for accessing and managing these resources:

!!! note "Access methods for private resources"
    - **AWS CloudShell**: Browser-based shell with pre-configured AWS CLI access
    - **AWS Session Manager**: Secure shell access to EC2 instances without bastion hosts
    - **Application Load Balancer**: For exposing applications publicly with proper security

### Using AWS CloudShell

AWS CloudShell provides a convenient way to interact with your AWS resources without needing local tools:

1. **Access CloudShell**: Available directly from the AWS Management Console
2. **Pre-configured environment**: AWS CLI and common tools already installed
3. **Automatic authentication**: Uses your current console session credentials
4. **Persistent storage**: 1GB of persistent storage for your files and scripts

For detailed guidance, see our [AWS CloudShell documentation](../tools/cloud-shell.md).

### Exposing applications publicly

To make your applications accessible from the internet:

1. **API Gateway** (recommended for APIs): Can connect to private VPC resources using VPC Links
2. **Application Load Balancer**: Use the [two-tier public routing architecture](networking.md#application-load-balancer-public-routing)
   - Deploy an internal ALB in your VPC
   - Tag it with `Public = True` to enable automatic public routing
   - Configure HTTPS listener (required for security)
   - Add health check endpoint at `/bcgovhealthcheck`

!!! success "Security best practice"
    Always use HTTPS for public endpoints. LZA provides default SSL certificates, or you can create custom certificates using AWS Certificate Manager.

## Development workflow

### Setting up your development environment

1. **Access your accounts**: Use the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts) to access your development environment
2. **Choose appropriate account**: 
   - **Development account**: For initial development and testing
   - **Test account**: For integration testing and staging
   - **Tools account**: For CI/CD pipelines and shared resources
   - **Production account**: For production deployments (requires Admin role)

### Infrastructure as Code

LZA strongly encourages Infrastructure as Code (IaC) for consistent and repeatable deployments.

Popular IaC tools supported in LZA:
- **[Terraform](https://www.terraform.io/)**: Multi-cloud infrastructure tool
- **[AWS CloudFormation](https://aws.amazon.com/cloudformation/)**: AWS native IaC service
- **[AWS CDK](https://aws.amazon.com/cdk/)**: Code-based infrastructure definition

For detailed guidance, see our [Infrastructure as Code best practices](../best-practices/iac-and-ci-cd.md).

### CI/CD Pipeline setup

Deploy your CI/CD pipelines in the **Tools account** for centralized management:

1. **GitHub Actions with OIDC**: Secure authentication without long-term credentials
2. **AWS CodePipeline**: Native AWS CI/CD service
3. **Cross-account deployments**: Use AWS APIs to deploy to other accounts

!!! warning "Network limitations"
    The Tools account VPC has no private network connectivity to Dev, Test, or Prod VPCs. Use API-based deployments only, or consider running pipelines directly in target accounts for VPC-specific resources.

## Monitoring and cost management

### Setting up monitoring

1. **AWS CloudWatch**: Monitor application metrics and set up alarms
2. **AWS CloudTrail**: Audit trail for all API calls (automatically configured)
3. **Application-specific monitoring**: Implement custom metrics for your applications

### Managing costs

1. **Budget alerts**: Configure through the [Product Registry](https://registry.developer.gov.bc.ca/login) and establish custom budget alerts directly within AWS as required
2. **Cost Explorer**: Use AWS Cost Explorer to analyze spending patterns
3. **Resource optimization**: Regularly review and right-size your resources

For detailed guidance, see our [AWS Cost Management documentation](../understanding-your-bill/aws-cost-management.md).

## Reference TechDocs pages

- [Networking within AWS LZA](networking.md)
- [Requirements and limitations](requirements.md)
- [Deployment guide](deploy-to-the-aws-landing-zone-accelerator.md)
- [User management](user-management.md)
- [Infrastructure as Code best practices](../best-practices/iac-and-ci-cd.md)
- [Security guardrails](../get-started-with-lza/guardrails.md)
- [AWS CloudShell](../tools/cloud-shell.md)
- [Session Manager](../tools/session-manager.md)

## Reference AWS training resources

- [Getting Started with AWS](https://aws.amazon.com/getting-started/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)
- [AWS VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)
- [AWS Application Load Balancer Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [AWS CloudFormation User Guide](https://docs.aws.amazon.com/cloudformation/)
- [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)
