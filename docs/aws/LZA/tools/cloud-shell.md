# AWS CloudShell

Last updated: **{{ git_revision_date_localized }}**

AWS CloudShell is a browser-based, pre-authenticated shell that you can launch directly from the AWS Console. It provides a convenient way to run AWS CLI commands, scripts, and other tools without needing to install or configure anything on your local machine.

CloudShell comes pre-installed with the AWS CLI, popular development tools, and runtimes, making it an ideal environment for managing AWS resources, running scripts, and performing administrative tasks directly from your web browser.

!!! info "Pre-configured Environment"
    CloudShell automatically authenticates using your current AWS Console session credentials, eliminating the need to configure AWS CLI credentials manually.

## Getting started with CloudShell

### Accessing CloudShell

1. **Sign in to AWS Console** - Access the [AWS SSO Portal](https://bcgov.awsapps.com/start/#/?tab=accounts) and select your account
2. **Launch CloudShell** - Click the CloudShell icon in the console toolbar or search for "CloudShell" in the services menu
3. **Wait for initialization** - CloudShell will launch in a new browser tab and initialize your environment
4. **Start using** - Begin running AWS CLI commands immediately

!!! tip "Console Integration"
    CloudShell inherits your current AWS Console session, including your region selection and account context, making it seamlessly integrated with your workflow.

### First-time setup

When you first launch CloudShell:

- **Environment creation** - AWS creates a persistent environment for your user
- **Home directory** - You get 1 GB of persistent storage in your home directory
- **Pre-installed tools** - Common tools and AWS CLI are ready to use
- **Session timeout** - Sessions timeout after periods of inactivity

## Features and capabilities

### Pre-installed Tools

CloudShell comes with many tools pre-installed:

- **AWS CLI v2** - Latest version of the AWS Command Line Interface
- **Development tools** - Git, Node.js, Python, Java, .NET Core
- **Text editors** - vim, nano, emacs for editing files
- **Shell environments** - Bash shell with common utilities
- **Container tools** - Docker CLI (for building containers)

### Persistent storage

- **Home directory** - 1 GB of persistent storage per user
- **File persistence** - Files in your home directory persist between sessions
- **Session isolation** - Each user has their own isolated environment
- **Automatic cleanup** - Inactive environments are cleaned up after extended periods

!!! warning "Storage Limitations"
    CloudShell provides 1 GB of persistent storage. Large files or extensive downloads may consume this space quickly. Monitor your usage with `df -h $HOME`.

### Security features

- **IAM integration** - Uses your current IAM permissions
- **Temporary credentials** - No long-term credentials stored
- **Session isolation** - Each user session is isolated
- **Audit logging** - All actions logged through CloudTrail

## LZA-specific considerations

### Regional availability

CloudShell is available in Canada Central region:

- **Canada Central** - Primary region for LZA workloads
- **Automatic region** - CloudShell operates in your selected console region
- **Cross-region access** - Can manage resources in any accessible region

### Network Access

In the LZA environment:

- **Internet access** - CloudShell has outbound internet connectivity
- **AWS API access** - Direct access to all AWS APIs and services
- **No VPC access** - Cannot directly access resources in private VPCs
- **Public endpoints only** - Can only reach publicly accessible resources

!!! info "VPC Resource Access"
    CloudShell cannot directly access resources in private VPCs. Use Session Manager or other tools for private resource access.

### Permission Model

CloudShell uses your existing IAM permissions:

- **Same permissions** - CloudShell has the same permissions as your console session
- **No additional access** - Cannot perform actions beyond your IAM permissions
- **Role assumption** - Can assume roles if you have permission
- **MFA integration** - Respects MFA requirements for sensitive operations

## Common use cases

### AWS CLI Operations

```bash
# List EC2 instances
aws ec2 describe-instances --region ca-central-1

# Create S3 bucket
aws s3 mb s3://my-unique-bucket-name-12345

# Deploy CloudFormation stack
aws cloudformation deploy --template-file template.yaml --stack-name my-stack

# Check IAM permissions
aws sts get-caller-identity
```

### Script Development and Testing

```bash
# Create a script
cat > deploy-app.sh << 'EOF'
#!/bin/bash
echo "Deploying application..."
aws s3 sync ./app s3://my-app-bucket/
aws cloudformation update-stack --stack-name my-app --template-body file://template.yaml
EOF

# Make executable and run
chmod +x deploy-app.sh
./deploy-app.sh
```

### File Management

```bash
# Download files from S3
aws s3 cp s3://my-bucket/config.json ./

# Edit configuration
nano config.json

# Upload modified file
aws s3 cp config.json s3://my-bucket/
```

### Git Operations

```bash
# Clone repository
git clone https://github.com/your-org/your-repo.git

# Make changes and commit
cd your-repo
git add .
git commit -m "Update configuration"
git push origin main
```

## Best practices

### File Management

- **Use home directory** - Store persistent files in `$HOME`
- **Clean up regularly** - Remove temporary files to conserve space
- **Backup important files** - Store critical files in S3 or Git repositories
- **Monitor disk usage** - Check available space with `df -h $HOME`

### Security

- **Avoid hardcoding credentials** - Use IAM roles and temporary credentials
- **Regular session cleanup** - Close unused sessions to prevent unauthorized access
- **Sensitive data handling** - Don't store sensitive information in CloudShell
- **Audit activity** - Review CloudTrail logs for CloudShell actions

### Performance

- **Session management** - Close inactive sessions to free resources
- **Large operations** - Use appropriate AWS services for large-scale operations
- **Network efficiency** - Minimize large downloads/uploads through CloudShell
- **Script optimization** - Optimize scripts for CloudShell's environment

## Limitations and considerations

### Technical Limitations

- **Compute resources** - Limited CPU and memory for intensive operations
- **Network restrictions** - Cannot access private VPC resources directly
- **Session timeout** - Sessions timeout after periods of inactivity
- **Storage limits** - 1 GB persistent storage per user

### LZA-specific Limitations

- **VPC isolation** - Cannot directly connect to private resources
- **Regional restrictions** - Limited to approved AWS regions
- **Service access** - Subject to LZA service restrictions and guardrails
- **Marketplace limitations** - Cannot install software from restricted sources

!!! warning "Production Workloads"
    CloudShell is designed for administrative tasks and development work, not for running production workloads or long-running processes.

## Troubleshooting

### Common Issues

**CloudShell won't launch:**
- Check your IAM permissions for CloudShell
- Verify you're in a supported region
- Clear browser cache and cookies

**Commands failing:**
- Verify your IAM permissions for the specific AWS service
- Check if the service is available in your current region
- Ensure you're using the correct AWS CLI syntax

**Storage full:**
```bash
# Check disk usage
df -h $HOME

# Find large files
du -h $HOME | sort -hr | head -10

# Clean up temporary files
rm -rf $HOME/.cache/*
```

**Session timeout:**
- CloudShell sessions timeout after 20 minutes of inactivity
- Save your work frequently
- Use persistent storage for important files

## Alternatives to CloudShell

For different use cases, consider these alternatives:

- **[Session Manager](session-manager.md)** - For accessing EC2 instances directly
- **Local AWS CLI** - For consistent local development environment
- **AWS Cloud9** - For more comprehensive cloud-based development
- **GitHub Codespaces** - For Git-integrated development environments

## Related AWS documentation

- [AWS CloudShell User Guide](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html)
- [CloudShell Features](https://docs.aws.amazon.com/cloudshell/latest/userguide/vm-specs.html)
- [CloudShell Security](https://docs.aws.amazon.com/cloudshell/latest/userguide/security.html)
- [AWS CLI v2 Reference](https://docs.aws.amazon.com/cli/latest/reference/)

## Related documentation

- [Helpful Tools](tools.md) - Overview of LZA development tools
- [Session Manager](session-manager.md) - Secure EC2 instance access
- [IaC and CI/CD](../best-practices/iac-and-ci-cd.md) - Infrastructure automation best practices
- [Requirements](../design-build-deploy/requirements.md) - Platform limitations and constraints
