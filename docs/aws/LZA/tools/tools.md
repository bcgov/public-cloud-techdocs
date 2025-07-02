# Helpful tools

Last updated: **{{ git_revision_date_localized }}**

This page is designed for those who manage and maintain AWS environments within the Landing Zone Accelerator (LZA). The purpose is to provide a curated list of tools that can help you efficiently manage your AWS resources, ensure compliance with best practices, and optimize your cloud infrastructure. Whether you are looking to automate tasks, monitor performance, or enhance security, the tools listed here will assist you in achieving your goals.

!!! info "AWS tools"
    It is important to note that these tools **do not** replace, but complement the [built-in AWS tools](../best-practices/governance.md), providing additional functionality and insights to enhance your AWS management experience.

## Platform-provided tools

The LZA platform includes several built-in tools for secure access and management:

### AWS CloudShell

Browser-based, pre-authenticated shell environment with AWS CLI and development tools pre-installed. Perfect for running AWS commands without local setup.

- **Access**: Available directly from the AWS Console
- **Pre-installed tools**: AWS CLI v2, Git, Python, Node.js, and more
- **Persistent storage**: 1 GB of persistent home directory storage
- **Documentation**: [AWS CloudShell guide](cloud-shell.md)

### AWS Systems Manager Session Manager

Secure, browser-based shell access to EC2 instances without SSH keys or bastion hosts. All instances in LZA are automatically configured for Session Manager access.

- **Security**: No open inbound ports or SSH keys required
- **Audit logging**: Full session logging and CloudTrail integration
- **Port forwarding**: Tunnel local ports to instance applications
- **Documentation**: [Session Manager guide](session-manager.md)

## Development and management tools

While there are many tools available to help you manage your AWS environment, the following are some that we have found to be particularly useful in the LZA context.

!!! abstract "Tools list"
    If you have a favorite tool that you think should be included in this list, please let us know by [creating a Support Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) with the details.

### AWS CLI

The official command-line interface for AWS services. Essential for automation and scripting in the LZA environment.

- **Installation**: `brew install awscli` (macOS) or [AWS CLI installation guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- **SSO integration**: Configure with `aws configure sso` for LZA access
- **Documentation**: [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)

```bash
# Configure AWS CLI for LZA SSO
aws configure sso
# SSO start URL: https://bcgov.awsapps.com/start
# SSO region: ca-central-1
```

### Terraform

Infrastructure as Code tool with excellent AWS provider support. Recommended for all infrastructure deployments in LZA.

- **Installation**: `brew install terraform` (macOS) or [Terraform installation](https://developer.hashicorp.com/terraform/install)
- **AWS Provider**: [Terraform AWS Provider documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- **LZA examples**: See our [sample code](../design-build-deploy/sample-code.md) for LZA-specific configurations

### AWS CDK (Cloud Development Kit)

Infrastructure as Code using familiar programming languages. Great for developers who prefer code over configuration files.

- **Installation**: `npm install -g aws-cdk` or [CDK installation guide](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html)
- **Languages supported**: TypeScript, Python, Java, C#, Go
- **Documentation**: [AWS CDK Developer Guide](https://docs.aws.amazon.com/cdk/v2/guide/)

### AWS Copilot

Command-line tool for building, releasing, and operating production-ready containerized applications on AWS App Runner, Amazon ECS, and AWS Fargate.

- **Installation**: `brew install aws/tap/copilot-cli` (macOS)
- **Use cases**: Container deployments, microservices, CI/CD pipelines
- **Documentation**: [AWS Copilot documentation](https://aws.github.io/copilot-cli/)

## Security and compliance tools

### AWS Config Rules Development Kit (RDK)

Tool for developing and deploying custom AWS Config rules for compliance monitoring.

- **Installation**: `pip install rdk`
- **Use cases**: Custom compliance rules, security monitoring
- **Documentation**: [AWS Config RDK](https://github.com/awslabs/aws-config-rdk)

### AWS Security Hub CLI

Command-line tool for managing Security Hub findings and insights.

- **Installation**: Available through AWS CLI
- **Use cases**: Security finding management, compliance reporting
- **Documentation**: [Security Hub CLI reference](https://docs.aws.amazon.com/cli/latest/reference/securityhub/)

### Prowler

Open-source security assessment tool for AWS environments.

- **Installation**: `pip install prowler-cloud`
- **Features**: CIS benchmarks, GDPR, HIPAA compliance checks
- **Documentation**: [Prowler documentation](https://docs.prowler.cloud/)

## Infrastructure scanning tools

Tools that scan Infrastructure as Code to identify security vulnerabilities, compliance issues, and cost optimization opportunities:

### Checkov

Static code analysis tool for Infrastructure as Code with extensive AWS support.

- **Installation**: `pip install checkov`
- **Features**: 1000+ built-in policies, custom rule support
- **Documentation**: [Checkov documentation](https://www.checkov.io/)

### TFLint

Terraform linter focused on possible errors and best practices.

- **Installation**: `brew install tflint` (macOS)
- **Features**: AWS-specific rules, custom rule support
- **Documentation**: [TFLint documentation](https://github.com/terraform-linters/tflint)

### Trivy

Comprehensive security scanner for containers and Infrastructure as Code.

- **Installation**: `brew install trivy` (macOS)
- **Features**: Vulnerability scanning, secret detection, misconfigurations
- **Documentation**: [Trivy documentation](https://trivy.dev/)

### InfraCost

Cost estimation tool for Terraform projects.

- **Installation**: `brew install infracost` (macOS)
- **Features**: Cost breakdown, budget alerts, CI/CD integration
- **Documentation**: [InfraCost documentation](https://www.infracost.io/)

## Monitoring and observability

### AWS CLI CloudWatch Insights

Query and analyze CloudWatch Logs using the AWS CLI.

```bash
# Example: Query application logs
aws logs start-query \
    --log-group-name /aws/lambda/my-function \
    --start-time $(date -d '1 hour ago' +%s) \
    --end-time $(date +%s) \
    --query-string 'fields @timestamp, @message | filter @message like /ERROR/'
```

### AWS Systems Manager Inventory

Collect metadata about your EC2 instances and installed software.

- **Access**: Through Systems Manager console or CLI
- **Use cases**: Software inventory, compliance reporting, patch management
- **Documentation**: [Systems Manager Inventory](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-inventory.html)

## Development productivity tools

### AWS Toolkit for VS Code

IDE extension providing AWS service integration directly in Visual Studio Code.

- **Installation**: Install from VS Code Extensions marketplace
- **Features**: SAM debugging, CloudFormation support, S3 browser
- **Documentation**: [AWS Toolkit for VS Code](https://docs.aws.amazon.com/toolkit-for-vscode/)

### AWS SAM CLI

Command-line tool for building and testing serverless applications.

- **Installation**: `brew install aws-sam-cli` (macOS)
- **Features**: Local testing, deployment, debugging
- **Documentation**: [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)

### LocalStack

Local AWS cloud stack for development and testing.

- **Installation**: `pip install localstack`
- **Use cases**: Local development, testing, CI/CD
- **Documentation**: [LocalStack documentation](https://docs.localstack.cloud/)

## Architecture and documentation

### AWS architecture icons

Official AWS icons for creating architecture diagrams.

- **Download**: [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/)
- **Formats**: PowerPoint, Visio, Draw.io, Sketch
- **Use cases**: Architecture diagrams, presentations, documentation

### AWS Well-Architected Tool

Framework for reviewing architectures against AWS best practices.

- **Access**: Through AWS Console
- **Features**: Automated reviews, improvement recommendations
- **Documentation**: [Well-Architected Tool](https://docs.aws.amazon.com/wellarchitected/latest/userguide/intro.html)

## Cost optimization tools

### AWS Cost Explorer CLI

Analyze AWS costs and usage through command-line interface.

```bash
# Example: Get cost by service for last month
aws ce get-cost-and-usage \
    --time-period Start=2024-01-01,End=2024-02-01 \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=SERVICE
```

### AWS Budgets

Set up cost and usage budgets with alerts.

- **Access**: Through AWS Console or CLI
- **Features**: Cost budgets, usage budgets, RI utilization budgets
- **Documentation**: [AWS Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)

## Getting started

1. **Start with platform tools** - Use CloudShell and Session Manager for immediate access
2. **Install AWS CLI** - Essential for automation and scripting
3. **Choose your IaC tool** - Terraform or CDK for infrastructure management
4. **Add security scanning** - Integrate tools like Checkov or Trivy into your workflow
5. **Monitor costs** - Use Cost Explorer and set up budgets

## Related documentation

- [AWS CloudShell](cloud-shell.md) - Browser-based AWS CLI access
- [Session Manager](session-manager.md) - Secure EC2 instance access
- [IaC and CI/CD](../best-practices/iac-and-ci-cd.md) - Infrastructure automation best practices
- [Sample Code](../design-build-deploy/sample-code.md) - LZA-specific examples and templates
