# Deploy an application to the  B.C. Government AWS Landing Zone
Last updated: **November 15, 2023**

The B.C. Government AWS Secure Environment Accelerator (ASEA) environment uses a multi-account architecture to provide secure and isolated environments for development, testing, production, and tools. This allows teams to safely build, test, and deploy applications without affecting live services.

This guide explains how to:

- Understand the AWS accounts in your project set
- Use Terraform to define your infrastructure as code
- Configure OIDC authentication for your GitHub Actions
- Manage Terraform state with S3 and DynamoDB
- Build CI/CD pipelines using GitHub Actions
- Follow tips and best practices for the ASEA

---

## Prerequisites

To follow this guide, you need:

- Access to a ASEA project set with dev, test, prod, and tools accounts
- The ability to create AWS resources like S3 buckets, DynamoDB tables, etc.
- A GitHub account with permissions to create repositories and workflows
- Basic knowledge of [Terraform](https://www.terraform.io/), [GitHub Actions](https://docs.github.com/en/actions), and the [AWS CLI](https://aws.amazon.com/cli/)

## AWS Accounts in your project set

The B.C. Government ASEA uses separate AWS accounts for development (dev), testing (test), and production (prod) environments. This isolates and protects each stage of the deployment lifecycle.

- The dev account is for developers to experiment and test features
- The test account mirrors production and is used for quality assurance testing
- The prod account is the live environment accessed by end users

A tools account contains shared resources like CI/CD pipelines, container registries, and automation tools.

## Defining your Infrastructure using Terraform

Terraform lets you define cloud resources in configuration files. This infrastructure as code approach helps manage resources consistently and repeatedly.

For example, to deploy a static website to AWS S3 and CloudFront:

```hcl
  # S3 bucket to host the website
  resource "aws_s3_bucket" "website" {
    bucket = "my-website-bucket"
  }
  
  # CloudFront distribution to cache and distribute the website 
  resource "aws_cloudfront_distribution" "website_dist" {
    origin {
      domain_name = aws_s3_bucket.website.bucket_regional_domain_name
      origin_id   = "my-website-s3-origin"

      s3_origin_config {
        origin_access_identity = aws_cloudfront_origin_access_identity.website_access_identity.cloudfront_access_identity_path
      }
    }
    
    enabled             = true
    default_root_object = "index.html"
    
    # ...other CloudFront settings  
    
    # Add origin access identity
    restrictions {
      geo_restriction {
        restriction_type = "none"
      }
    }
  }

  # CloudFront origin access identity
  resource "aws_cloudfront_origin_access_identity" "website_access_identity" {
    comment = "CloudFront origin access identity for my website"
  }
```

With this approach, you can version your infrastructure, collaborate with others, and repeatedly deploy your app.

## Configuring GitHub Action OIDC Authentication to AWS

To allow GitHub Actions to securely access AWS accounts, use OpenID Connect (OIDC) authentication.

For detailed instructions, see the [GitHub Actions OIDC Authentication Guide](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services).

Here's a quick summary on how to set it up:

1. The GitHub Identity Provider has already been configured in the AWS accounts in your project set
2. In your AWS account:
   - Create an IAM role for GitHub Actions
   - Create a trust policy on the role with the GitHub Identity Provider
   - Attach a policy that allows access to the AWS resources that you need
3. In GitHub workflows, configure AWS credentials with the IAM role ARN that you created.
   - See [Writing GitHub Action Workflows](#writing-github-action-workflows) below for an example

This allows GitHub Actions to assume the IAM role and access your AWS accounts.

## Using S3 and DynamoDB for Terraform State

To collaborate on Terraform state:

1. Create an S3 bucket to store state files
2. Enable versioning on the S3 bucket
3. Create a DynamoDB table for state locking and consistency
4. Configure Terraform to use the S3/DynamoDB backend:

```hcl
terraform {
    backend "s3" {
      # configuration details go here
      # or can be generated dynamically
    }
}
```

By leaving the backend configuration details undefined they can be generated dynamically by the GitHub Action workflow. This allows the same workflow and Terraform configuration to be used for multiple environments.

See [Writing GitHub Action Workflows](#writing-github-action-workflows) below for an example.

## Writing GitHub Action Workflows

Use GitHub Actions to build CI/CD pipelines for automated deployments. For example:

```yaml
name: Deploy to dev
on:
  push:
    branches:
      - main

permissions:
  id-token: write   # This is required for OIDC
  contents: read    # This is required for actions/checkout

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-region: ca-central-1
          role-to-assume: arn:aws:iam::<dev-aws-account-id>:role/my-github-actions-role # This is the IAM role created for GitHub Actions
          session-name: my-github-actions-session # This is the session name for the assumed role that will show up in CloudTrail logs
      
      - name: Generate backend config
        run: |
          cat <<EOF > backend.hcl
          bucket         = "dev-terraform-state-bucket"
          key            = "dev/my-app/terraform.tfstate"
          region         = "ca-central-1" 
          dynamodb_table = "terraform-lock-table"
          EOF
      
      - name: Init Terraform
        run: |
          terraform init -backend-config=backend.hcl
      
      - name: Apply Terraform
        run: |
          terraform apply -auto-approve
      
      - name: Deploy to dev
        run: |
          aws s3 sync --delete index.html s3://my-website-bucket
          aws cloudfront create-invalidation --distribution-id my-website-s3-origin --paths "/*"
```

## Exposing your application to the internet

For more complex applications, AWS API Gateway is the preferred method for exposing your application to the internet. It provides a scalable and managed service for creating, deploying, and managing APIs. If you have VPC resources, you can use API Gateway with VPC Link to connect the API Gateway to VPC resources like internal load balancers. This allows you to securely expose your application to the internet while leveraging the benefits of your VPC infrastructure.

Examples of using API Gateway with VPC Link can be found in the the [sample applications](#sample-applications).

## Tips and best practices

- Follow Hashicorp's [Terraform best practices guide](https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices)
- Review GitHub Actions [security hardening guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
- Use Terraform modules to reuse and share infrastructure definitions
- Perform security scans, penetration testing for production infrastructure
- Monitor costs, set budgets, alarms in AWS to avoid unexpected spend

## Sample applications

The Public Cloud team has created sample applications to demonstrate various application architectures. These sample applications are available on GitHub:

- [AWS Serverless](https://github.com/bcgov/startup-sample-project-aws-serverless-OIDC)
- [AWS Containers](https://github.com/bcgov/startup-sample-project-aws-containers)
- [AWS Virtual Machines](https://github.com/bcgov/startup-sample-project-aws-virtual-machines)
