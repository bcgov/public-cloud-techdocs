# B.C. Government OCIO AWS Landing Zone overview

Last updated: **{{ git_revision_date_localized }}**

An overview of the B.C. Government OCIO's Landing Zone in AWS, how to get access, its benefits, components, and features.

---

## Benefits of building apps in the AWS Public Cloud

Ministry teams in the B.C. government who want to build applications in the Public Cloud can rely on the OCIO's secure and compliant landing zone in AWS. It offers a robust, secure, and efficient framework designed  to meet the needs and compliance requirements of the BC government. This ensures that applications are developed within a secure and well-governed cloud environment. The OCIO's AWS landing zone offers several significant benefits:

1. **Enhanced security compliance**: It aligns with high-standard security frameworks like NIST 800-53 and the CCCS Medium Cloud Control Profile, which is crucial for government applications handling sensitive, Protected B data. This compliance ensures that your applications meet the necessary security standards, providing peace of mind for both developers and stakeholders

2. **Streamlined development process**: It helps ministry teams concentrate on developing applications instead of managing foundational infrastructure by automating various aspects of the cloud environment setup. This boosts the speed of government application development

3. **Customizable and scalable architecture**: It gives you the freedom to customize the cloud architecture for your project, supporting a variety of applications. Whether you're working on small projects or large, complex applications, it adapts to meet your needs

4. **Operational consistency and governance**: By using OCIO's landing zone, you adopt a uniform approach to cloud infrastructure, ensuring consistency. This uniformity is vital for upholding operational standards and governance.

5. **Long-term management and evolution**: This tool not only helps deploy applications initially but also aids in their ongoing management and evolution. This feature is crucial for government applications, which frequently need updates and adjustments to align with changing policy requirements and citizen needs

6. **Leveraging cutting-egde capabilities in public cloud**: When you build in public cloud, they can tap into a wide range of services and capabilities, spanning from advanced analytics to AI and machine learning tools. This integration has the potential to greatly boost the functionality and reach of government applications

For B.C. government ministry teams developing applications in the AWS Public Cloud, the OCIO landing zone provides a secure, compliant, and efficient pathway. This facilitates the creation of innovative and responsive applications that effectively serve the public.

## How to get access

Explore a [comprehensive guide](https://digital.gov.bc.ca/cloud/services/public/onboard) for onboarding your team to the public cloud.

## Components and features

In this section, we'll provide a high level overview of the components and features of the OCIO's Landing Zone in AWS.

### Platform Product Registry

The Platform Product Registry service is a comprehensive solution designed to streamline the process of requesting and creating AWS Project Sets for B.C. government ministry teams. Each Project Set comprises four distinct AWS accounts: Development (dev), Testing (test), Production (prod), and Tools. This service plays a crucial role, not just in setting up the necessary AWS infrastructure, but also in managing various aspects of a product's lifecycle in the cloud.

### Key Features of the Platform Product Registry Service

1. **AWS Project Set creation**
   - Helps create a set of four AWS accounts (Dev, Test, Prod, Tools) customized for different stages of the application development lifecycle
   - Guarantees a structured and consistent approach to AWS account management, crucial for large-scale and complex cloud environments

2. **Product ownership and technical leadership**
   - Enables the definition and documentation of Product Owners and Technical Leads for each product
   - Improves accountability and role clarity, ensuring that each product has designated points of contact and leadership

3. **Budget management**
   - Empowers the setting of budgets on each AWS account within a Project Set
   - Provides alerts when predefined budget thresholds are reached, aiding in effective financial management and cost control

4. **User access management**
   - Manages user access to the AWS accounts, ensuring that only authorized personnel have access to the respective development, testing, production, and tooling environments
   - Improves security and governance by regulating access based on roles and responsibilities

The Product Registry service represents a significant step forward in managing cloud resources effectively, offering a centralized, automated, and controlled approach to handle various aspects of AWS account management, from initial setup to continuous budgeting and access control. This service is particularly helpful for teams needing to manage many cloud-based products, providing a streamlined and secure solution for AWS cloud management.

### Project Set

A project set consists of four distinct AWS accounts for development (dev), testing (test), production (prod), and tools (tools) environments. This isolates and protects each stage of the deployment lifecycle.

- The dev account is for developers to experiment and test features
- The test account mirrors production and is used for quality assurance testing
- The prod account is the live environment accessed by end users
- The tools account contains shared resources like CI/CD pipelines, container registries, and automation tools

### Security guardrails

The AWS Secure Environment Accelerator (ASEA) product provides a security framework for B.C. government ministry teams developing applications in the AWS Public Cloud. This framework includes both preventative and detective controls to ensure a secure and compliant cloud environment.

#### Preventative controls

- **Service Control Policies (SCPs)**
  - SCPs act as guardrails to protect the architecture and prevent critical guardrails from being turned off
  - They can deny specific or entire categories of API operations at an AWS account, organizational unit (OU), or organization level
  - SCPs ensure workloads are deployed only in prescribed regions (ca-central-1) and restrict access to specific AWS services as necessary

- **AWS Config**
  - AWS Config is crucial for keeping an eye on and evaluating AWS resource configurations
  - It makes sure that the resources follow the specified policies and standards
  - AWS Config works seamlessly with various other AWS services, giving a complete picture of the AWS environment and helping maintain the desired configuration

#### Detective controls

1. **AWS Security Hub**
   - Security Hub offers a centralized dashboard for assessing the security posture of the AWS footprint
   - It aggregates findings from various AWS security services like Amazon GuardDuty, AWS Config, and others
   - Security Hub frameworks such as AWS Foundational Security Best Practices, PCI DSS, and CIS AWS Foundations Benchmark are recommended to be enabled for comprehensive checks against AWS Config resources

2. **Amazon GuardDuty**
   - GuardDuty is a threat detection service that  monitors for malicious activity and unauthorized behavior
   - It uses machine learning, anomaly detection, and integrated threat intelligence to identify and prioritize potential threats
   - GuardDuty is required to be enabled at the organization level, with the security account as the administrative account

3. **Amazon Macie**
   - Macie is an AWS service focused on data security and privacy
   - It uses machine learning and pattern matching to discover and protect sensitive data stored in AWS

4. **AWS Config (Extended Role)**
   - In addition to its role in preventative controls, AWS Config also plays a part in detective controls
   - It supports the detection of non-compliant resources and configurations, contributing to overall security monitoring

The ASEA security framework ensures that you can develop and deploy applications in a secure, compliant, and controlled AWS environment, enabling them to focus on delivering innovative and effective digital services.

For more information, see [AWS Security & Compliance Guardrails](./security-guardrails.md).

### Networking

For B.C. government ministry teams developing applications in the OCIO's landing zone, understanding the high-level overview of the networking components of the AWS Secure Environment Accelerator (ASEA) is essential. These components are designed to ensure secure, efficient, and compliant networking within AWS environments.

#### High-Level Overview of Networking Components

- **Perimeter account**

The Perimeter Account hosts the Perimeter Virtual Private Cloud (VPC), which controls the flow of traffic between AWS accounts and external networks for Infrastructure as a Service (IaaS) workloads. This includes both internet and, in some cases, private networks (e.g., access to on-premises data centers). The Perimeter VPC hosts AWS Network Firewall and/or third-party Next Generation Firewalls (NGFW) like CheckPoint, providing comprehensive perimeter security services, including virus scanning, malware protection, intrusion protection services, TLS inspection, and Web Application Firewall protection.

- **Shared network account**

The Shared Network account centralizes the AWS-side of the networking resources. It hosts workload-scoped VPCs (such as Development, Test, Production, etc.), which are deployed within this account and shared via AWS Resource Access Manager (RAM) to the respective workload accounts based on their organizational units (OUs). This centralization eliminates the need to create duplicate networking resources across multiple accounts, reducing operational overhead.

- **Workload accounts**

In each workload account, matching various stages of the software development life cycle (Dev/Test/Prod), there are VPCs and subnets shared from the Shared Network account. These connect to the organization's network through the centralized transit gateway.

**Exposing services to the internet**

- **API Gateway and VPC Link**

For applications that require exposure to the internet, services like AWS API Gateway can be used in conjunction with VPC Link. The API Gateway acts as a managed service to handle HTTP requests and responses, while VPC Link securely connects the API Gateway to the back-end services hosted within the VPCs. This is the recommended approach for most applications.

- **Application Load Balancers (ALBs)**

The Perimeter account includes ALBs for production and Dev/Test workloads. These ALBs facilitate the distribution of incoming application traffic across multiple targets, such as EC2 instances. The use of AWS Web Application Firewall (WAF) on both front-end and back-end ALBs enhances security, controlling traffic and protecting against common web exploits. This is not recommended for most applications unless there is a specific need to use ALBs.

<!-- - TODO: Link to the networking page -->

### Logging

For B.C. government ministry teams working in the OCIO's landing zone, it's crucial to understand the high-level overview of the logging components integrated into the ASEA. These components include CloudWatch, CloudTrail, and a centralized Log Archive, each playing a vital role in maintaining a secure and compliant cloud environment.

#### CloudWatch Logs

- **What is CloudWatch Logs**: CloudWatch Logs, a part of AWS CloudWatch, focuses on log management. It enables collection, monitoring, and analysis of log data from AWS resources and applications. Ideal for DevOps, developers, and IT managers, it provides essential capabilities for real-time log monitoring and operational troubleshooting
  
- **Immutability of Log Groups**: In the ASEA architecture, CloudWatch Logs groups are rendered immutable due to Service Control Policies (SCPs). This immutability is crucial for maintaining the integrity and security of log data, ensuring logs are preserved in their original state for security audits and compliance purposes

- **Retention Policy**: The retention period for CloudWatch Logs groups is automatically set to 30 days. This duration is optimal for regular monitoring and immediate operational analysis. For long-term retention and audit purposes, logs are sent to a centralized log archive using Amazon Kinesis, where they are retained for 2 years. This two-tiered approach to log retention ensures that operational needs are met while also maintaining compliance with data retention policies

#### CloudTrail

- **What is it**: AWS CloudTrail is a service that provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting
- **Trails are Immutable**: CloudTrail is configured to send logs directly to S3 for centralized immutable log retention. The trail itself cannot be modified or deleted by any principal in any child account, providing an audit trail for detective purposes and ensuring the integrity of log files
- **Retention**: CloudTrail logs are maintained with high integrity, with versioning and SCPs (Service Control Policies) ensuring that deleted or overwritten files are retained. This makes it difficult to modify, delete, or forge CloudTrail log files without detection

#### Log Archive

- **What is Stored in the Central Log Archive**: The Log Archive account centralizes and stores immutable logs for the organization. It contains centralized storage for copies of every account’s audit, configuration, compliance, and operational logs, as well as other audit/compliance logs and application/OS logs
- **Retention**: The Log Archive account is designed for long-term retention and immutable storage of logs. The emphasis on immutability and restricted access underlines the importance of log integrity and security in compliance and forensic analysis

This logging architecture ensures that ministry teams can effectively monitor, audit, and analyze activities within their AWS environment, thereby maintaining a high level of security and compliance.

### IAM Users (service accounts)

The IAM User Management and Key Rotation solution, an integral part of the B.C. Government AWS Landing Zone, offers a secure and automated method for managing IAM users and their access keys. This solution is needed for scenarios where access to AWS services is required from outside the AWS environment, such as from on-premises systems.

For detailed user documentation, see the [IAM User Management Service](../design-build-and-deploy-an-application/iam-user-service.md) page.

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

### Understanding ASEA-protected resources in your AWS accounts

#### Overview

Within your AWS environment managed by the AWS Secure Environment Accelerator (ASEA), you'll encounter a range of resources created and maintained by ASEA. These resources are critical for the security, compliance, and efficiency of your cloud operations. It's important for ministry teams to recognize these resources and understand their purpose.

By recognizing and respecting the role of these ASEA-managed resources, ministry teams can effectively navigate and utilize their AWS environment within the boundaries of established best practices and governance policies.

#### ASEA-protected resources

- **Identifying ASEA resources**: Resources created by ASEA are typically identifiable through specific naming conventions and tags. They often start with the prefix `PBMM`, or carry tags such as `AcceleratorName: PBMM` or `Accelerator: PBMM`
- **Nature of resources**: These resources could range from network configurations, security groups, and IAM roles, to monitoring tools and logging services. They are integral to maintaining the operational standards set by ASEA

#### Protection and restrictions

- **Protected by SCPs**: These resources are safeguarded by Service Control Policies (SCPs). This layer of protection ensures that the resources remain intact and are not altered, preventing any unintended security or compliance breaches
- **Impact on ministry teams**: As a result of these protections, ministry teams will generally have limited, read-only access to these ASEA-managed resources. Modification or removal is restricted to maintain the integrity of the cloud environment

#### Role of ASEA-managed resources

- **Security and compliance**: These resources are pivotal in ensuring that your AWS environment adheres to the high standards of security and compliance required by the B.C. government
- **Operational efficiency**: By standardizing and automating key aspects of cloud management, these resources help in maintaining operational efficiency and consistency across different ministry teams

## Next steps

- [Deploy an application to the B.C. Government AWS Landing Zone](../design-build-and-deploy-an-application/deploy-an-app-to-the-aws-landing-zone.md)

## Related pages

- [Public cloud services](https://digital.gov.bc.ca/cloud/services/public)
- [Public cloud hosting 101](https://digital.gov.bc.ca/cloud/services/public/intro/)
- [Deploy an application to the B.C. Government AWS Landing Zone](../design-build-and-deploy-an-application/deploy-an-app-to-the-aws-landing-zone.md)
- [IAM User Management Service](../design-build-and-deploy-an-application/iam-user-service.md)
