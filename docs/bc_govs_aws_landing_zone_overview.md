# B.C. Government AWS Landing Zone Overview

Last updated: **November 10, 2023**

An overview of the B.C. Government AWS Landing Zone, who it is for, how to get access, its benefits, components, and features.

## On this page

- [On this page](#on-this-page)
- [Introduction](#introduction)
- [How to get access](#how-to-get-access)
- [Components and features](#components-and-features)
  - [Product registry](#product-registry)
  - [Key Features of the Product Registry Service](#key-features-of-the-product-registry-service)
  - [Project Set](#project-set)
  - [Security Guardrails](#security-guardrails)
  - [Networking](#networking)
  - [Logging](#logging)
  - [IAM Users (service accounts)](#iam-users-service-accounts)
  - [Next steps](#next-steps)
- [Related pages](#related-pages)

## Introduction

For Ministry teams within the BC Government looking to build applications in the AWS Public Cloud, the AWS Secure Environment Accelerator (ASEA) provides a robust, secure, and efficient framework. This tool is especially tailored to meet the unique needs and compliance requirements of government entities, ensuring that applications are developed within a secure and well-governed cloud environment.

From this perspective, the ASEA offers several significant benefits:

1. **Enhanced Security Compliance**: ASEA aligns with high-standard security frameworks like NIST 800-53 and the CCCS Medium Cloud Control Profile, which is crucial for government applications handling sensitive, Protected B data. This compliance ensures that your applications meet the necessary security standards, providing peace of mind for both developers and stakeholders.

1. **Streamlined Development Process**: By automating many aspects of the cloud environment setup, ASEA allows Ministry teams to focus more on application development rather than foundational infrastructure management. This efficiency can significantly accelerate the development cycle of government applications.

1. **Customizable and Scalable Architecture**: ASEA provides the flexibility to tailor the cloud architecture to specific project needs, accommodating a wide range of applications. Whether you are working on small-scale projects or extensive, multi-faceted applications, ASEA scales to meet these demands.

1. **Operational Consistency and Governance**: Implementing ASEA ensures a consistent approach to cloud infrastructure across different Ministry teams. This consistency is key in maintaining operational standards and governance, crucial for government operations.

1. **Long-term Management and Evolution**: The tool not only assists in the initial deployment of applications but also supports their long-term management and evolution. This feature is vital for government applications, which often require ongoing updates and adaptations to meet changing policy requirements and citizen needs.

1. **Leveraging AWS Capabilities**: By building on AWS, Ministry teams can take advantage of the vast array of services and capabilities offered by AWS, from advanced analytics to AI and machine learning tools. This integration can significantly enhance the functionality and reach of government applications.

For BC Government Ministry teams developing applications in the AWS Public Cloud, the ASEA offers a secure, compliant, and efficient pathway, facilitating the creation of innovative and responsive applications that serve the public effectively.

## How to get access

A detailed guide on how to onboard your team to public cloud is available here:

<https://digital.gov.bc.ca/cloud/services/public/onboard>

## Components and features

In this section, we'll provide a high level overview of the components and features of the B.C. Government AWS Landing Zone.

### Product registry

The "Product Registry" service is a comprehensive solution designed to streamline the process of requesting and creating AWS "Project Sets" for BC Government Ministry teams. Each Project Set comprises four distinct AWS accounts: Development (dev), Testing (test), Production (prod), and Tools. This service is not only instrumental in setting up the required AWS infrastructure but also plays a vital role in managing various aspects of a product's lifecycle in the cloud.

### Key Features of the Product Registry Service

1. **AWS Project Set Creation**:
   - Facilitates the creation of a set of four AWS accounts (Dev, Test, Prod, Tools) tailored for different stages of the application development lifecycle.
   - Ensures a structured and consistent approach to AWS account management, essential for large-scale and complex cloud environments.

2. **Product Ownership and Technical Leadership**:
   - Allows for the definition and documentation of Product Owners and Technical Leads for each product.
   - Enhances accountability and clarity in roles, ensuring that each product has designated points of contact and leadership.

3. **Budget Management**:
   - Enables setting budgets on each AWS account within a Project Set.
   - Provides alerts when predefined budget thresholds are reached, aiding in effective financial management and cost control.

4. **User Access Management**:
   - Manages user access to the AWS accounts, ensuring that only authorized personnel have access to the respective development, testing, production, and tooling environments.
   - Enhances security and governance by controlling access based on roles and responsibilities.

The Product Registry service represents a significant step forward in managing cloud resources effectively, offering a centralized, automated, and controlled approach to handle various aspects of AWS account management, from initial setup to ongoing budget and access control. This service is particularly beneficial for Ministry teams needing to manage multiple cloud-based products, providing a streamlined and secure solution for AWS cloud management.

### Project Set

A project set consists of four separate AWS accounts for development (dev), testing (test), production (prod), and Tools (tools) environments. This isolates and protects each stage of the deployment lifecycle.

- The dev account is for developers to experiment and test features
- The test account mirrors production and is used for quality assurance testing
- The prod account is the live environment accessed by end users
- The tools account contains shared resources like CI/CD pipelines, container registries, and automation tools.

### Security Guardrails

The AWS Secure Environment Accelerator (ASEA) provides a comprehensive security framework for BC Government Ministry teams developing applications in the AWS Public Cloud. This framework includes both preventative and detective controls to ensure a secure and compliant cloud environment.

#### Preventative Controls

1. **Service Control Policies (SCPs)**:
   - SCPs act as guardrails to protect the architecture and prevent the disablement of critical guardrails.
   - They can deny specific or entire categories of API operations at an AWS account, organizational unit (OU), or organization level.
   - SCPs ensure workloads are deployed only in prescribed regions (ca-central-1) and restrict access to specific AWS services as necessary.

2. **AWS Config**:
   - AWS Config plays a key role in monitoring and assessing the AWS resource configurations.
   - It helps ensure that the resources are compliant with the prescribed policies and standards.
   - AWS Config integrates with various other AWS services to provide a comprehensive view of the AWS environment, helping in maintaining the desired configuration state.

#### Detective Controls

1. **AWS Security Hub**:
   - Security Hub offers a centralized dashboard for assessing the security posture of the AWS footprint.
   - It aggregates findings from various AWS security services like Amazon GuardDuty, AWS Config, and others.
   - Security Hub frameworks such as AWS Foundational Security Best Practices, PCI DSS, and CIS AWS Foundations Benchmark are recommended to be enabled for comprehensive checks against AWS Config resources.

2. **Amazon GuardDuty**:
   - GuardDuty is a threat detection service that continuously monitors for malicious activity and unauthorized behavior.
   - It uses machine learning, anomaly detection, and integrated threat intelligence to identify and prioritize potential threats.
   - GuardDuty is required to be enabled at the organization level, with the security account as the administrative account.

3. **Amazon Macie**:
   - Macie is an AWS service focused on data security and privacy.
   - It uses machine learning and pattern matching to discover and protect sensitive data stored in AWS.

4. **AWS Config (Extended Role)**:
   - In addition to its role in preventative controls, AWS Config also plays a part in detective controls.
   - It supports the detection of non-compliant resources and configurations, contributing to overall security monitoring.

The ASEA security framework ensures that Ministry teams can develop and deploy applications in a secure, compliant, and controlled AWS environment, enabling them to focus on delivering innovative and effective digital services.

- TODO: Link to the security guardrails page

### Networking

For BC Government Ministry teams developing applications in the AWS Public Cloud, understanding the high-level overview of the networking components of the AWS Secure Environment Accelerator (ASEA) is essential. These components are designed to ensure secure, efficient, and compliant networking within AWS environments.

#### High-Level Overview of Networking Components

##### Perimeter Account

The Perimeter Account hosts the Perimeter Virtual Private Cloud (VPC), which controls the flow of traffic between AWS accounts and external networks for Infrastructure as a Service (IaaS) workloads. This includes both internet and, in some cases, private networks (e.g., access to on-premises data centers). The Perimeter VPC hosts AWS Network Firewall and/or third-party Next Generation Firewalls (NGFW) like CheckPoint, providing comprehensive perimeter security services, including virus scanning, malware protection, intrusion protection services, TLS inspection, and Web Application Firewall protection.

##### Shared Network Account

The Shared Network account centralizes the AWS-side of the networking resources. It hosts workload-scoped VPCs (such as Development, Test, Production, etc.), which are deployed within this account and shared via AWS Resource Access Manager (RAM) to the respective workload accounts based on their organizational units (OUs). This centralization eliminates the need to create duplicate networking resources across multiple accounts, reducing operational overhead.

##### Workload Accounts

Each workload account, corresponding to different stages of the software development life cycle (Dev/Test/Prod), contains VPCs and subnets which are shared from the Shared Network account. These are connected to the organization's network via the centralized transit gateway.

**Exposing Services to the Internet**:

- **API Gateway and VPC Link**: For applications that require exposure to the internet, services like AWS API Gateway can be used in conjunction with VPC Link. The API Gateway acts as a managed service to handle HTTP requests and responses, while VPC Link securely connects the API Gateway to the back-end services hosted within the VPCs. This is the recommended approach for most applications.
- **Application Load Balancers (ALBs)**: The Perimeter account includes ALBs for production and Dev/Test workloads. These ALBs facilitate the distribution of incoming application traffic across multiple targets, such as EC2 instances. The use of AWS Web Application Firewall (WAF) on both front-end and back-end ALBs enhances security, controlling traffic and protecting against common web exploits. This is not recommend for most applications unless there is a specific need to use ALBs.

- TODO: Link to the networking page

### Logging

For BC Government Ministry teams working in the AWS Public Cloud, it's crucial to understand the high-level overview of the logging components integrated into the ASEA. These components include CloudWatch, CloudTrail, and a centralized Log Archive, each playing a vital role in maintaining a secure and compliant cloud environment.

#### CloudWatch Logs

- **What is CloudWatch Logs**: CloudWatch Logs, a part of AWS CloudWatch, focuses on log management. It enables collection, monitoring, and analysis of log data from AWS resources and applications. Ideal for DevOps, developers, and IT managers, it provides essential capabilities for real-time log monitoring and operational troubleshooting.
  
- **Immutability of Log Groups**: In the ASEA architecture, CloudWatch Logs groups are rendered immutable due to Service Control Policies (SCPs). This immutability is crucial for maintaining the integrity and security of log data, ensuring logs are preserved in their original state for security audits and compliance purposes.

- **Retention Policy**: The retention period for CloudWatch Logs groups is automatically set to 30 days. This duration is optimal for regular monitoring and immediate operational analysis. For long-term retention and audit purposes, logs are sent to a centralized log archive using Amazon Kinesis, where they are retained for 2 years. This two-tiered approach to log retention ensures that operational needs are met while also maintaining compliance with data retention policies.

#### CloudTrail

- **What is it**: AWS CloudTrail is a service that provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting.
- **Trails are Immutable**: CloudTrail is configured to send logs directly to S3 for centralized immutable log retention. The trail itself cannot be modified or deleted by any principal in any child account, providing an audit trail for detective purposes and ensuring the integrity of log files.
- **Retention**: CloudTrail logs are maintained with high integrity, with versioning and SCPs (Service Control Policies) ensuring that deleted or overwritten files are retained. This makes it difficult to modify, delete, or forge CloudTrail log files without detection.

#### Log Archive

- **What is Stored in the Central Log Archive**: The Log Archive account centralizes and stores immutable logs for the organization. It contains centralized storage for copies of every account’s audit, configuration, compliance, and operational logs, as well as other audit/compliance logs and application/OS logs.
- **Retention**: The Log Archive account is designed for long-term retention and immutable storage of logs. The emphasis on immutability and restricted access underlines the importance of log integrity and security in compliance and forensic analysis.

This logging architecture ensures that Ministry teams can effectively monitor, audit, and analyze activities within their AWS environment, thereby maintaining a high level of security and compliance.

### IAM Users (service accounts)

The IAM User Management and Key Rotation solution, an integral part of the BC Government AWS Landing Zone, offers a secure and automated method for managing IAM users and their access keys. This solution is needed for scenarios where access to AWS services is required from outside the AWS environment, such as from on-premises systems.

#### Summary of Features

1. **Automated IAM User Creation and Management**:
   - IAM users are created by inserting a new item into the DynamoDB table `BCGOV_IAM_USER_TABLE`.
   - The Lambda function triggers to create the IAM user, generate an access key, and store it in the SSM Parameter Store.

2. **Key Rotation**:
   - Automated key rotation is managed by the Lambda function.
   - Keys older than 2 days are marked for deletion and replaced with new keys.
   - The keys reach a 'pending_deletion' state after 2 days and are fully deleted after 4 days.

3. **Access Key Retrieval and Storage**:
   - Keys are automatically stored in the SSM Parameter Store in a structured JSON format.
   - Users can retrieve these keys for automation purposes.

4. **Automation Compatibility**:
   - Users can incorporate a checker in their scripts to identify and switch from 'pending_deletion' keys to 'current' keys, ensuring they always use the latest keys.

5. **Permission Boundaries for Security**:
   - The Lambda function sets permission boundaries to prevent privilege escalation.
   - This ensures users have only the necessary permissions, even if their IAM policies allow more.

6. **Deletion of IAM Users**:
   - Users can be deleted by removing their entry from the DynamoDB table.
   - The Lambda function will then automatically delete the user and their keys from IAM and the SSM Parameter Store.

7. **Best Practices for Secure Operation**:
   - Apply least-privilege permissions, granting only necessary permissions for tasks.
   - Implement identity-based policies to restrict access based on the source IP, enhancing security.

This solution facilitates efficient IAM user management and enhances security by ensuring proper key rotation and management of permissions, aligning with best practices for secure AWS operations.

### Next steps

- [Deploy an application to the B.C. Government AWS Landing Zone](/docs/deploy_an_app_to_bc_govs_aws_landing_zone.md)

## Related pages

- [Public cloud services](https://digital.gov.bc.ca/cloud/services/public)
- [Public cloud hosting 101](https://digital.gov.bc.ca/cloud/services/public/intro/)
- [Deploy an application to the B.C. Government AWS Landing Zone](/docs/deploy_an_app_to_bc_govs_aws_landing_zone.md)
