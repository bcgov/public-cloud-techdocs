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

<<<<<<< Updated upstream
keywords: example 1, example 2, example 3


page_purpose: Why are you writing this documentation?


audience: Who will be reading the documentation? example 1, example 2, example 3 


author: Writers involved in the creation of the document 

Editor: Content Strategist / Content Reviewer 


content_owner: Name of Subject matter expert


sort_order: What is order it should be displayed on the menu? 1 or 2 or 3 or etc.
=======
# Whoever wrote the original draft
author: Abibat
# The subject matter expert of the page. They are responsible for the factual accuracy of the content.
content_owner: Pilar Solares Velasco
# A positive integer used to determine the sort order of the page within a navigation menu category. If left blank, the page will be sorted alphabetically at the end of the sorted list within a menu.
sort_order: 1
>>>>>>> Stashed changes
---

# Page title in sentence case

This is introductory text that should go before your table of contents for best search result quality. It might replace your `description` frontmatter field in search results if Google decides it's better.

## On this page

- [Introduction](#Introduction)
- [Prerequisites](#Prerequisites)
- [Third content heading](#Prerequisites)
- [Fourth content heading](#Prerequisites)
- [Fifth content heading](#Prerequisites)
- [Sixth content heading](#Prerequisites)
- [Seventh content heading](#Prerequisites)
- [Eighth content heading](#Prerequisites)

- [Related links](#related-links)

---

Body of Document


## First H2 content heading in sentence case
1. Introduction
### First H3 content heading
- Brief overview of the BC Government AWS SEA environment.
The AWS Accelerator is a tool designed to help deploy and operate secure multi-account, multi-region AWS environments on an ongoing basis. The power of the solution is the configuration file that drives the architecture deployed by the tool. This enables extensive flexibility and for the completely automated deployment of a customized architecture within AWS without changing a single line of code.
Deploys an opinionated and prescriptive architecture designed to help meet the security and operational requirements of many governments around the world (initial focus was the Government of Canada).​The Cloud Pathfinder team has added additional automation, and functionality to meet the needs of the Government of BC
### Second H3 content heading
- Explanation of the project-set concept: dev, test, prod, and tools AWS accounts.
### Third H3 content heading
- Purpose of the technical documentation.
This technical documentation is to basically give insight to allow the technical team 

## Second H2 content heading in sentence case
2. Prerequisites
List of prerequisites for deploying the app, such as AWS accounts, permissions, and tools.

## Third H2 content heading in sentence case
3. Defining your Infrastructure using Terraform
### Fourth H3 content heading
- What is Terraform
Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. 
Sample app(AWS CloudFront and S3)

## Fourth H2 content heading in sentence case
4. Configuring GitHub Action OIDC Authentication to AWS

### Fifth H3 content heading
- Explanation of using OpenID Connect (OIDC) for GitHub Action authentication to AWS.
Open ID Connect (OIDC) is a set of protocols that simplifies the authorization process between
two entities that trust each other. When configuring in AWS to use OIDC the first requirement is to have the OIDC component available. When creating the project set for the different ministry teams, the Cloud Pathfinder Team (CPF) adds the OIDC component to the list of components available in AWS to the ministry teams so it is ready for use. The ministry teams are responsible to configure their AWS and GitHub environments to use OIDC

### Sixth H3 content heading
- Step-by-step guide on setting up OIDC authentication.

### Seventh H3 content heading
- How to configure AWS credentials in a GitHub Action.

### Eighth H3 content heading
- Validation and testing of the authentication setup.

## Fifth H2 content heading in sentence case
5. Using S3 and DynamoDB for Terraform State

### Nineth H3 content heading
- Introduction to Terraform state management.

### Tenth H3 content heading
- Explanation of using Amazon S3 and DynamoDB for storing Terraform state files.
Using Amazon S3 and DynamoDB for storing Terraform state files is a best practice approach to managing and maintaining the state of your infrastructure as code deployments. Terraform is a tool for provisioning and managing cloud infrastructure resources, and its state files keep track of the current state of the resources it manages.

Amazon S3 (Simple Storage Service) is a scalable and durable object storage service, while Amazon DynamoDB is a fully managed NoSQL database service. Combining these two services with Terraform helps ensure the reliability, security, and collaboration of your infrastructure deployments. Here's how it works:
S3 for Storing State Files:
When Terraform runs, it generates a state file that reflects the current state of your infrastructure. This state file is crucial because it helps Terraform understand what resources are already deployed, what changes need to be made, and what resources need to be created, modified or destroyed.
DynamoDB for Locking:
When Terraform runs, it reads the current state file to determine the existing resources. During this process, it also locks the state to prevent concurrent modifications by multiple users. This is important to avoid conflicting changes and potential resource corruption
### Eleventh H3 content heading
- Walkthrough of setting up S3 bucket and DynamoDB table for Terraform state storage.

### Twelveth H3 content heading
- How to configure Terraform to use the S3 and DynamoDB backend.

## Sixth H2 content heading in sentence case
6. Writing GitHub Action Workflows

### Thirteenth H3 content heading
- Overview of GitHub Actions and their importance in the deployment process.
GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.
https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions

### Fourteenth H3 content heading
- Explanation of the provided workflows in the sample app repository.

### Fifteenth H3 content heading
- Detailed breakdown of each workflow's purpose and steps.
   Workflow for deploying to a development environment.
   Workflow for testing the application.
   Workflow for deploying to production environment.

### Sixteenth H3 content heading
- Instructions for modifying or creating new workflows based on requirements.

### Seventeenth H3 content heading
- Step-by-step guide on deploying the sample app to the BC Government SEA.

### Eighteenth H3 content heading
- Instructions for configuring environment-specific variables and settings.

### Nineteenth H3 content heading
- How to trigger the deployment using GitHub Actions.
https://docs.github.com/en/actions/deployment/about-deployments/deploying-with-github-actions

### Twenteenth H3 content heading
- Monitoring and troubleshooting deployment issues.

## Seventh H2 content heading in sentence case
7. Tips & Best Practices

###  H3 content heading
- Some of these could be links to external documentation
https://www.terraform-best-practices.com/
https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices
https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions

###  H3 content heading
- How to run a Terraform Plan from your local development environment

###  H3 content heading
- Tips and best practices for managing resources in the BC Government SEA environment.

###  H3 content heading
- Recommendations for maintaining security and compliance standards.

###  H3 content heading
- Guidelines for updating and scaling the deployed application.

## Eighth H2 content heading in sentence case
8. Sample Applications

###  H3 content heading
- Links to the three sample apps
https://github.com/bcgov/startup-sample-project-aws-containers
https://github.com/bcgov/startup-sample-project-aws-serverless-OIDC
https://github.com/bcgov/startup-sample-project-aws-virtual-machines

## Related links

- [Example.org](https://example.org)








