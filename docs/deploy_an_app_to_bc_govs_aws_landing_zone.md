---
title: How to Deploy an App in the BC Government SEA


slug: How-to-Deploy-an-App-in-the-BC-Government-SEA


Description: Brief explanation of what will a reader find in this particular doc


keywords: example 1, example 2, example 3


page_purpose: Why are you writing this documentation?


audience: Who will be reading the documentation? example 1, example 2, example 3 


author: Writers involved in the creation of the document 
j
Editor: Content Strategist / Content Reviewer 


content_owner: Name of Subject matter expert


sort_order: What is order it should be displayed on the menu? 1 or 2 or 3 or etc.
---

# Main title
Last updated: **Month, Day, Year**
{{Write a small paragraph about the content of the document. Introduce to your readers what the page will be about. This should only be a 5-6 paragraph description.}}

<!-- 
## On this page 
* [**Introduction**](#this-is-an-example)
* [**Prerequisites**](#this-is-another-example)
* [**Configuring GitHub Action OIDC Authentication to AWS**](#)
* [****](#)
* [**Related Pages**](#related-pages)
 -->


## 1. Introduction
Brief overview of the BC Government AWS SEA environment.
The AWS Accelerator is a tool designed to help deploy and operate secure multi-account, multi-region AWS environments on an ongoing basis. The power of the solution is the configuration file that drives the architecture deployed by the tool. This enables extensive flexibility and for the completely automated deployment of a customized architecture within AWS without changing a single line of code.
Deploys an opinionated and prescriptive architecture designed to help meet the security and operational requirements of many governments around the world (initial focus was the Government of Canada).​The Cloud Pathfinder team has added additional automation, and functionality to meet the needs of the Government of BC
Explanation of the project-set concept: dev, test, prod, and tools AWS accounts.
Purpose of the technical documentation.
This technical documentation is to basically give insight to allow the technical team 
## 2. Prerequisites
List of prerequisites for deploying the app, such as AWS accounts, permissions, and tools.
Deployment into AWS infrastructure includes having AWS account, Github and the required AWS IAM permissions 

## 3. Defining your Infrastructure using Terraform
What is Terraform
Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. 
Sample app(AWS CloudFront and S3)


## 4. Configuring GitHub Action OIDC Authentication to AWS
Explanation of using OpenID Connect (OIDC) for GitHub Action authentication to AWS.
Open ID Connect (OIDC) is a set of protocols that simplifies the authorization process between
two entities that trust each other. When configuring in AWS to use OIDC the first requirement is to have the OIDC component available. When creating the project set for the different ministry teams, the Cloud Pathfinder Team (CPF) adds the OIDC component to the list of components available in AWS to the ministry teams so it is ready for use. The ministry teams are responsible to configure their AWS and GitHub environments to use OIDC.
Step-by-step guide on setting up OIDC authentication.
How to configure AWS credentials in a GitHub Action.
Validation and testing of the authentication setup.

## 5. Using S3 and DynamoDB for Terraform State
Introduction to Terraform state management.
Explanation of using Amazon S3 and DynamoDB for storing Terraform state files.

Using Amazon S3 and DynamoDB for storing Terraform state files is a best practice approach to managing and maintaining the state of your infrastructure as code deployments. Terraform is a tool for provisioning and managing cloud infrastructure resources, and its state files keep track of the current state of the resources it manages.

Amazon S3 (Simple Storage Service) is a scalable and durable object storage service, while Amazon DynamoDB is a fully managed NoSQL database service. Combining these two services with Terraform helps ensure the reliability, security, and collaboration of your infrastructure deployments. Here's how it works:
S3 for Storing State Files:
When Terraform runs, it generates a state file that reflects the current state of your infrastructure. This state file is crucial because it helps Terraform understand what resources are already deployed, what changes need to be made, and what resources need to be created, modified or destroyed.
DynamoDB for Locking:
When Terraform runs, it reads the current state file to determine the existing resources. During this process, it also locks the state to prevent concurrent modifications by multiple users. This is important to avoid conflicting changes and potential resource corruption

Walkthrough of setting up S3 bucket and DynamoDB table for Terraform state storage.
How to configure Terraform to use the S3 and DynamoDB backend.

## 6. Writing GitHub Action Workflows
Overview of GitHub Actions and their importance in the deployment process.
GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.
https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions
Explanation of the provided workflows in the sample app repository.
Detailed breakdown of each workflow's purpose and steps.
Workflow for deploying to a development environment.
Workflow for testing the application.
Workflow for deploying to production environment.
Instructions for modifying or creating new workflows based on requirements.
Step-by-step guide on deploying the sample app to the BC Government SEA.
Instructions for configuring environment-specific variables and settings.
How to trigger the deployment using GitHub Actions.
Monitoring and troubleshooting deployment issues.
## 8. Tips & Best Practices
Some of these could be links to external documentation
How to run a Terraform Plan from your local development environment
Tips and best practices for managing resources in the BC Government SEA environment.
Recommendations for maintaining security and compliance standards.
Guidelines for updating and scaling the deployed application.

## 8. Sample Applications
Links to the three sample apps

## This is another example of a section

{{Content goes here}}

## Related Pages 

[Text](link) 



Tips for writing technical documentation: 

Focus on the subject and introduce the reader to dive deeper. Plain language is encouraged even for a technical audience. This plain language and checklist is something worth checking out as it can save you time when writing, the steps are simple and it’s worth having it handy if you need further help.

Examples and use cases, provide real-world examples and use cases related to the topic if available. 

Diagrams and visual aids. Don’t know how to explain something? Have a picture? Diagram? Video that can explain it better? - have free reign to do it. If you are recording a video, make sure they are no longer than 5 minutes. 

Related pages can be a section to include other documentation that can help a visitor dive deeper or connect with another supplemental subject.

Don’t get caught up in the styling of the document, this can be worked at the end and can be done by the UX Content strategist. It is encouraged to check spelling and peer review (min 2 people) with team members before any documentation is live.   

Current & old content
https://digital.gov.bc.ca/cloud/public/onboard/#project



https://github.com/bcgov/startup-sample-project-aws-containers
