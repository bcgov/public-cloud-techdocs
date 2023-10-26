---
# Page title in sentence case, used to generate <title> tag
title: Deploy an app

# Slug is used to generate the page path in the URL. Please use lowercase and separate words with -. Ex: Using `slug: landing-page` will cause the page to appear on the Gatsby site at /landing-page/.
slug: deploy-an-app

# A brief, precise description of what a reader will find on the page. Used to generate the <meta name="description"> tag.
# How to write a good meta description: https://developers.google.com/search/docs/appearance/snippet
description: A good page description should be unique and succinctly describe the content the user will find on the page.

# Used to generate the <meta property="keywords"> tag. Could be used in the future to group related content.
keywords: first keyword, second keyword

# This is a more in depth description that isn't used on the rendered page. We can go into more details about why a page needs to exist here, compared to the "description" field which should be for the end-user's benefit.
page_purpose: The page purpose doesn't appear on the rendered page like the "description" field, but it lets us add context to a document and why it exists.

# Typically, a developer or a technical lead. Not used on the rendered page.
audience: developer

# Whoever wrote the original draft
author: Abibat
# The subject matter expert of the page. They are responsible for the factual accuracy of the content.
content_owner: Pilar Solares Velasco
# A positive integer used to determine the sort order of the page within a navigation menu category. If left blank, the page will be sorted alphabetically at the end of the sorted list within a menu.
sort_order: 1
---




---
## On this page

- [Introduction](#Introduction)
- [Prerequisites](#Prerequisites)
- [Defining your Infrastructure using Terraform](#Defining-your-Infrastructure)
- [Configuring GitHub Action OIDC Authentication to AWS](#Configuring-GitHub-Action-OIDC-Authentication-to-AWS)
- [Using S3 and DynamoDB for Terraform State](#Using-S3-and-DynamoDB-for-Terraform-State)
- [Writing GitHub Action Workflows](#Writing-GitHub-Action-Workflows)
- [Tips & Best Practices](#Tips-&-Best-Practices)
- [Sample Applications](#Sample-Applications)


---




## Introduction

### Brief overview of the BC Government AWS SEA environment.
The AWS Accelerator is a tool designed to help deploy and operate secure multi-account, multi-region AWS environments on an ongoing basis. The power of the solution is the configuration file that drives the architecture deployed by the tool. This enables extensive flexibility and for the completely automated deployment of a customized architecture within AWS without changing a single line of code.
Deploys an opinionated and prescriptive architecture designed to help meet the security and operational requirements of many governments around the world (initial focus was the Government of Canada).​The Cloud Pathfinder team has added additional automation, and functionality to meet the needs of the Government of BC

### Explanation of the project-set concept: dev, test, prod, and tools AWS accounts.
The concept of project-set, which includes different AWS accounts for development (dev), testing (test), production (prod), and tools, is a best practice used to organize and manage resources and workloads in an AWS (Amazon Web Services) environment. This approach helps ensure separation, security, and isolation of environments while also improving resource management, access control, and collaboration. Here's an explanation of each of these AWS accounts within a project-set:

Development (Dev) AWS Account:

The Development account is where software engineers and developers work on new features and code changes. It is the earliest stage in the software development lifecycle.
Key characteristics:
Developers have the freedom to experiment and test without affecting the production environment.
It may contain multiple environments for different feature branches or teams.
Resources in this account should be isolated from production to prevent accidental disruptions.
Testing (Test) AWS Account:

The Testing account is where quality assurance (QA) engineers and testers validate and verify the functionality of the application.
Key characteristics:
It mirrors the production environment as closely as possible.
Testers can perform various types of testing, such as functional, load, and security testing.
Data and configurations should be as close to the production environment as possible to ensure accurate testing.
Production (Prod) AWS Account:

The Production account is the live environment where the application or service is accessible to end-users.
Key characteristics:
Strict access controls and monitoring are in place to maintain a highly secure and stable environment.
It is critical to minimize changes and disruptions to this environment to ensure high availability and reliability.
Disaster recovery and backup mechanisms should be well-defined in case of outages.
Tools AWS Account:

The Tools account is a separate AWS account used for various operational and tooling purposes.
Key characteristics:
It may include resources for CI/CD (Continuous Integration/Continuous Deployment), monitoring, logging, automation, and management.
It is isolated from the other accounts to prevent tool-related issues from affecting the dev, test, or prod environments.
Provides a centralized location for common tools and services used across the project-set.

###  Purpose of the technical documentation.
This technical documentation is to basically give insight to allow the technical team 

## Prerequisites
List of prerequisites for deploying the app, such as AWS accounts, permissions, and tools.

## Defining your Infrastructure using Terraform

### What is Terraform
Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. 
Sample app(AWS CloudFront and S3)

## Configuring GitHub Action OIDC Authentication to AWS

### Explanation of using OpenID Connect (OIDC) for GitHub Action authentication to AWS.
Open ID Connect (OIDC) is a set of protocols that simplifies the authorization process between
two entities that trust each other. When configuring in AWS to use OIDC the first requirement is to have the OIDC component available. When creating the project set for the different ministry teams, the Cloud Pathfinder Team (CPF) adds the OIDC component to the list of components available in AWS to the ministry teams so it is ready for use. The ministry teams are responsible to configure their AWS and GitHub environments to use OIDC

### Step-by-step guide on setting up OIDC authentication.

### How to configure AWS credentials in a GitHub Action.

### Validation and testing of the authentication setup.

## Using S3 and DynamoDB for Terraform State

### Introduction to Terraform state management.

### Explanation of using Amazon S3 and DynamoDB for storing Terraform state files.
Using Amazon S3 and DynamoDB for storing Terraform state files is a best practice approach to managing and maintaining the state of your infrastructure as code deployments. Terraform is a tool for provisioning and managing cloud infrastructure resources, and its state files keep track of the current state of the resources it manages.

Amazon S3 (Simple Storage Service) is a scalable and durable object storage service, while Amazon DynamoDB is a fully managed NoSQL database service. Combining these two services with Terraform helps ensure the reliability, security, and collaboration of your infrastructure deployments. Here's how it works:
S3 for Storing State Files:
When Terraform runs, it generates a state file that reflects the current state of your infrastructure. This state file is crucial because it helps Terraform understand what resources are already deployed, what changes need to be made, and what resources need to be created, modified or destroyed.
DynamoDB for Locking:
When Terraform runs, it reads the current state file to determine the existing resources. During this process, it also locks the state to prevent concurrent modifications by multiple users. This is important to avoid conflicting changes and potential resource corruption
### Walkthrough of setting up S3 bucket and DynamoDB table for Terraform state storage.

### How to configure Terraform to use the S3 and DynamoDB backend.

## Writing GitHub Action Workflows

### Overview of GitHub Actions and their importance in the deployment process.
GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.
https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions

### Explanation of the provided workflows in the sample app repository.

### Detailed breakdown of each workflow's purpose and steps.
#### Workflow for deploying to a development environment.
#### Workflow for testing the application.
#### Workflow for deploying to production environment.

### Instructions for modifying or creating new workflows based on requirements.

### Step-by-step guide on deploying the sample app to the BC Government SEA.

### Instructions for configuring environment-specific variables and settings.

### How to trigger the deployment using GitHub Actions.
[github docs](https://docs.github.com/en/actions/deployment/about-deployments/deploying-with-github-actions)

### Monitoring and troubleshooting deployment issues.

## Tips & Best Practices

###  Some of these could be links to external documentation
https://www.terraform-best-practices.com/
https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices
https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions

### How to run a Terraform Plan from your local development environment
To run a Terraform plan from your local development environment, you need to have Terraform, AWS CLI and VScode(most widely used) installed and properly configured.You also need to have the reqired permission to deploy to AWS, Copy and paste credentials from AWS login for the respective account, commands into your shell to set up your AWS CLI environment variables. Here are the steps to run a Terraform plan:

Install Terraform:
If you haven't already installed Terraform on your local development environment, you can download the latest version from the official Terraform website [terraform install](https://www.terraform.io/downloads.html) and follow the installation instructions for your operating system.

Initialize Your Terraform Configuration:
Open a terminal and navigate to the directory where your Terraform configuration files are located. Use the `terraform init` command to initialize Terraform. This step downloads any required providers and sets up your working directory.

Run Terraform Plan:
Now, you can run the `terraform plan` command to generate an execution plan based on your configuration. The plan command provides a preview of the changes that Terraform will make to your infrastructure

Review the Plan:
Terraform will display a summary of the changes it intends to make. This includes resources to be created, modified, or deleted. Carefully review the plan to ensure it aligns with your expectations and requirements.

Apply the Plan:
If you are satisfied with the plan and want to apply the changes to your infrastructure, you can use the `terraform apply` command. Be cautious when applying changes, especially in a production environment, as it can make significant modifications.

### Tips and best practices for managing resources in the BC Government SEA environment.

### Recommendations for maintaining security and compliance standards.

### Guidelines for updating and scaling the deployed application.

## Sample Applications

### Links to the three sample apps
https://github.com/bcgov/startup-sample-project-aws-containers
https://github.com/bcgov/startup-sample-project-aws-serverless-OIDC
https://github.com/bcgov/startup-sample-project-aws-virtual-machines

## Related links

- [Example.org](https://example.org)








