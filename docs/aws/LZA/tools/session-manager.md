# AWS Systems Manager Session Manager

Last updated: **{{ git_revision_date_localized }}**

AWS Systems Manager Session Manager is a fully managed service that provides secure, browser-based shell access to EC2 instances without requiring SSH keys, bastion hosts, or open inbound ports. Session Manager enables secure connections to instances through private IP addresses, making it ideal for the LZA's security-focused environment.

Session Manager provides secure and seamless shell connectivity to your EC2 instances directly through the AWS Console, AWS CLI, or SDK, eliminating the need for traditional SSH/RDP access methods that require network connectivity.

!!! info "Security Benefits"
    Session Manager eliminates the need for SSH keys, bastion hosts, or open inbound security group rules, significantly reducing your attack surface while providing full audit logging of all session activity.

## Prerequisites

### Instance requirements

Before using Session Manager, ensure your EC2 instances meet these requirements:

- **SSM Agent installed** - Most modern AWS AMIs include SSM Agent by default
- **IAM instance profile** - Instance must have an IAM role with Session Manager permissions
- **Network connectivity** - Instance must be able to reach Systems Manager endpoints

!!! info "Automatic Configuration in LZA"
    All virtual machines in the LZA environment are automatically configured with Session Manager permissions on their default instance roles. If you create custom IAM roles for your instances, you must include the Session Manager permissions.

### Required IAM permissions

For reference, Session Manager requires these permissions (automatically configured in LZA):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:UpdateInstanceInformation",
                "ssmmessages:CreateControlChannel",
                "ssmmessages:CreateDataChannel",
                "ssmmessages:OpenControlChannel",
                "ssmmessages:OpenDataChannel"
            ],
            "Resource": "*"
        }
    ]
}
```

!!! tip "Platform-Managed Permissions"
    These permissions are automatically included in the default instance roles for EC2 instances. When creating custom IAM roles, ensure you include these permissions or attach the `AmazonSSMManagedInstanceCore` managed policy.

## Accessing instances via Session Manager

### Through AWS console

1. **Navigate to EC2 Console** - Go to the [EC2 Instances page](https://ca-central-1.console.aws.amazon.com/ec2/home?region=ca-central-1#Instances:)
2. **Select your instance** - Choose the instance you want to connect to
3. **Click Connect** - Use the "Connect" button in the instance details
4. **Choose Session Manager** - Select the "Session Manager" tab
5. **Start session** - Click "Connect" to open the browser-based terminal

### Through AWS CLI

Install and configure the Session Manager plugin for AWS CLI:

#### macOS installation (recommended)

```bash
# Install via Homebrew (easiest method)
brew install --cask session-manager-plugin

# Verify installation
session-manager-plugin --version

# Start a session
aws ssm start-session --target i-1234567890abcdef0 --region ca-central-1
```

#### macOS manual installation (alternative)

```bash
# Download and install manually
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/mac/sessionmanager-bundle.zip" -o "sessionmanager-bundle.zip"
unzip sessionmanager-bundle.zip
sudo ./sessionmanager-bundle/install -i /usr/local/sessionmanagerplugin -b /usr/local/bin/session-manager-plugin

# Clean up
rm -rf sessionmanager-bundle.zip sessionmanager-bundle/
```

!!! tip "Installation Methods"
    **Homebrew is recommended** for macOS users as it handles updates automatically. For other operating systems or alternative installation methods, see the [AWS documentation for Session Manager plugin installation](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html).

## Session Manager features

### Secure Shell Access

- **No SSH keys required** - Authentication handled through AWS IAM
- **No open ports** - No inbound security group rules needed
- **Encrypted connections** - All traffic encrypted using TLS 1.2
- **Private IP only** - Works with instances that have no public IP addresses

### Audit and Logging

Session Manager provides comprehensive logging capabilities:

- **Session history** - Track who accessed which instances and when
- **CloudTrail integration** - All Session Manager API calls logged
- **CloudWatch Logs** - Optional session transcript logging
- **S3 storage** - Store session logs in S3 for long-term retention

!!! example "Enabling Session Logging"
    Configure session logging through Systems Manager Preferences to capture all session activity for compliance and security auditing.

### Port Forwarding

Session Manager supports port forwarding for accessing applications:

```bash
# Forward local port 8080 to instance port 80
aws ssm start-session --target i-1234567890abcdef0 \
    --document-name AWS-StartPortForwardingSession \
    --parameters '{"portNumber":["80"],"localPortNumber":["8080"]}' \
    --region ca-central-1
```

## LZA-specific considerations

### Network Connectivity

In the LZA environment:

- **VPC endpoints** - Session Manager uses VPC endpoints for secure communication
- **No internet gateway required** - Works entirely through AWS private network
- **Firewall compatibility** - Traffic flows through managed VPC endpoints, compatible with LZA security controls

### Security Group Configuration

Session Manager requires **no inbound rules** in security groups:

- **Outbound HTTPS** - Allow outbound traffic on port 443 to Systems Manager endpoints
- **No SSH/RDP ports** - Traditional remote access ports (22, 3389) not required
- **Simplified security** - Reduces security group complexity

!!! warning "Outbound Access Required"
    Instances must have outbound HTTPS (port 443) access to AWS Systems Manager endpoints. This is typically allowed by default in LZA security groups.

### IAM Integration

Session Manager integrates with LZA's Entra ID-based access control:

- **IAM roles** - Use IAM roles for instance permissions
- **User permissions** - Control user access through IAM policies
- **Entra ID integration** - Leverage existing identity management

## Terraform configuration examples

### Using Default Instance Role

Here's a basic Terraform configuration using the default instance role (Session Manager permissions included automatically):

```hcl
# EC2 instance - Session Manager permissions automatically configured
resource "aws_instance" "example" {
  ami                    = data.aws_ami.amazon_linux.id
  instance_type          = "t3.micro"
  subnet_id              = var.subnet_id
  vpc_security_group_ids = [aws_security_group.example.id]
  
  tags = {
    Name = "SessionManager-Example"
  }
}
```

### Using Custom Instance Role

If you need a custom IAM role, ensure it includes Session Manager permissions:

```hcl
# Custom IAM role with Session Manager permissions
resource "aws_iam_role" "custom_role" {
  name = "CustomEC2Role"
  
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })
}

# Attach Session Manager managed policy
resource "aws_iam_role_policy_attachment" "ssm_managed_instance_core" {
  role       = aws_iam_role.custom_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}

# Add your custom policies here
resource "aws_iam_role_policy_attachment" "custom_policy" {
  role       = aws_iam_role.custom_role.name
  policy_arn = var.custom_policy_arn
}

# Instance profile for the custom role
resource "aws_iam_instance_profile" "custom_profile" {
  name = "CustomEC2Profile"
  role = aws_iam_role.custom_role.name
}

# EC2 instance with custom role
resource "aws_instance" "custom_example" {
  ami                    = data.aws_ami.amazon_linux.id
  instance_type          = "t3.micro"
  iam_instance_profile   = aws_iam_instance_profile.custom_profile.name
  subnet_id              = var.subnet_id
  vpc_security_group_ids = [aws_security_group.example.id]
  
  tags = {
    Name = "CustomRole-Example"
  }
}

# Security group allowing outbound HTTPS for Systems Manager
resource "aws_security_group" "example" {
  name        = "session-manager-sg"
  description = "Security group for Session Manager access"
  vpc_id      = var.vpc_id

  egress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
    description = "HTTPS outbound for Systems Manager"
  }

  tags = {
    Name = "SessionManager-SG"
  }
}
```

!!! warning "Custom Role Requirements"
    When creating custom IAM roles for EC2 instances, you **must** include Session Manager permissions by attaching the `AmazonSSMManagedInstanceCore` managed policy or equivalent custom permissions.

## Troubleshooting

### Instance not appearing

If your instance doesn't appear in Session Manager:

1. **Check SSM Agent** - Ensure SSM Agent is installed and running
2. **Network connectivity** - Ensure outbound HTTPS access to Systems Manager endpoints
3. **Region consistency** - Verify you're in the correct AWS region (Canada Central)
4. **Instance state** - Confirm the instance is running and has been up for a few minutes

### Connection issues

Common connection problems and solutions:

- **Timeout errors** - Check VPC endpoints and network connectivity
- **Permission denied** - Review your user IAM permissions for Session Manager access
- **Agent issues** - Restart SSM Agent on the instance

!!! tip "SSM Agent Status"
    Check SSM Agent status with: `sudo systemctl status amazon-ssm-agent` (Linux) or through Services (Windows).

## Best practices

### Security

- **Regular updates** - Keep SSM Agent updated on all instances
- **Least privilege** - Grant minimal required permissions
- **Session logging** - Enable comprehensive session logging for audit trails
- **Regular reviews** - Periodically review access patterns and permissions

### Operational

- **Consistent tagging** - Tag instances for easy identification in Session Manager
- **Documentation** - Document instance access procedures for your team
- **Automation** - Use Systems Manager for automated patching and maintenance
- **Monitoring** - Set up CloudWatch alarms for session activity

## Related AWS documentation

- [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
- [Session Manager Prerequisites](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-prerequisites.html)
- [Installing Session Manager Plugin](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html)
- [Session Manager Logging](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-logging.html)

## Related documentation

- [Helpful Tools](tools.md) - Overview of LZA development tools
- [AWS CloudShell](cloud-shell.md) - Browser-based AWS CLI access
- [Requirements](../design-build-deploy/requirements.md) - Platform limitations and constraints
- [Networking](../design-build-deploy/networking.md) - LZA network architecture
