# Networking
Last updated: **December 20, 2023**

## Introduction
This document breaks down the centralized networking of the AWS Secure Environment Accelerator (ASEA), and best practices for networking on the platform. It aims to make the ASEA's networking easy to grasp, helping anyone working on the platform (and within the guardrails of the BC Gov ASEA) understand how our networking is set up. Good networking is like the backbone of cloud technology. It connects everything, keeps things safe, and helps things run smoothly. Understanding it is crucial for making the most of cloud tools.



<!-- - Objectives of the document
- Importance of networking in cloud development and operations -->

## Network architecture

The ASEA network centers around a Transit Gateway, efficiently directing traffic between the internet, and AWS services in various different AWS Accounts on the platform. Networking is broken up into two separate accounts: 

- The Perimeter Account houses our Checkpoint Firewalls which monitor and restrict traffic in and out of the platform. 
- The Shared Network Account houses the Transit Gateway and acts as a central hub. this account houses all of the VPCs (except for the Perimeter VPC in the Perimeter account) and most other networking resources. These are made available via AWS Resource Access Manager (RAM) to the appropriate accounts based on their OU in the organization. Security Groups are then put in place restrict traffic between resources deployed in the VPCs. All this eliminates the need to create duplicate resources in multiple accounts, reducing the operational overhead of managing those resources in every single account.

The reason for the separation between the Shared Networking and Perimeter accounts is to facilitate networking and security "separation of duties". In summary, the ASEA's networking architecture ensures centralized, organized and secure communication through Transit Gateway routing, separated Perimeter VPC security, and Shared Network resource management as shown below in BC Gov ASEA's complete networking diagram:

![networking-architecture](images/networking/network-architecture.png)

For further reading beyond this document please visit the [AESA network architecture docs](https://aws-samples.github.io/aws-secure-environment-accelerator/latest/architectures/sensitive/network/). Note that we use the [GWLB architecture](https://aws-samples.github.io/aws-secure-environment-accelerator/latest/architectures/sensitive/diagrams/#14-additional-perimeter-patterns) with third-party firewalls.



<!-- - High-level overview of the ASEA network architecture
- network architecture diagram is available in sharepoint
- link to the AESA network architecture docs (We use the GWLB architecture with third-party firewalls) -->

## Gateway load balancers and CheckPoint firewalls

In the Perimeter account we use a [Gateway Load Balancer (GWLB)](https://aws.amazon.com/elasticloadbalancing/gateway-load-balancer/) to distribute traffic load between our firewall instances. These run on EC2, in a highly available pair. We use Checkpoint Firewalls from the AWS Marketplace, alongside the Checkpoint Firewall Manager where we use Checkpoint's Smart Console to set up traffic rules. Only HTTP/HTTPS traffic is allowed through the firewalls, all other traffic is blocked. This includes SSH egress traffic which can cause issues with accessing git repositories and other services that use SSH. To access git repositories, use HTTPS instead of SSH.



<!-- - Architecture and deployment of Gateway Load Balancers in the Perimeter Account
- Integration of CheckPoint Firewalls with Gateway Load Balancers
We use third party firewalls from Checkpoint running on EC2 to monitor traffic.  
- Only HTTP/HTTPS traffic is routed through the firewalls
  - This means that SSH egress traffic is blocked which can cause issues with accessing git repositories and other services that use SSH.
    - To access git repositories, use HTTPS instead of SSH -->

## Transit gateway

the Transit Gateway plays a central role as a key component for managing connectivity between different environments. The Transit Gateway serves several crucial functions within the ASEA architecture:

- **Centralized Routing Hub:** The Transit Gateway acts as a centralized hub for routing traffic. It facilitates communication between various environments, such as on-premises networks, the internet, and different Virtual Private Clouds (VPCs) within the platform. It utilizes route tables, and TGW policies to determine specific, allowed traffic within the platform.

- **Routing Permitted Flows:** It routes permitted flows, directing traffic between different zones based on defined policies. All VPCs are connected to the TGW and all traffic in and out of them must go through it. Note that there is a specific route via the Perimeter VPC that connects workload accounts to any connected on-premises networks without going to the internet.

- **Traffic Control and Policy Enforcement:** It enables the control and enforcement of traffic policies across the network. This is essential for maintaining security and ensuring that data flows adhere to predefined rules. These policies enforce security and isolation by specifying which environments or VPCs can communicate with each other. Communication between VPCs in not allowed in the ASEA, only communication from a VPC to the internet (or on-prem) or vice versa is allowed by the transit gateway. For example, Dev traffic destined for the Prod VPC will be blocked by the "blackhole" route in the TGW route table. Even Dev traffic that leaves the Dev VPC will not be allowed back into the Dev VPC directly from the TGW.

In summary, the Transit Gateway in ASEA LZ networking acts as a centralized and efficient routing hub, enabling secure communication and policy enforcement between various components within the ASEA.



<!-- - Role of Transit Gateway in ASEA LZ networking
- Configuration details: attachment to VPCs, route tables, and transit gateway policies
- How Transit Gateway facilitates inter-VPC communication and connectivity to on-premises networks -->

## Workload VPCs

Workload VPCs are strategically structured for Development (Dev), Testing (Test), Production (Prod), and Tools (Tools) environments, each assigned a /16 CIDR block. These VPCs leverage AWS Resource Access Manager (RAM) for efficient resource sharing, streamlining operations and enabling centralized management across organizational units. This configuration ensures secure and scalable hosting, supporting various stages of development and testing within the AWS cloud infrastructure.

- **Structure and Purpose of Workload-Specific VPCs:**
  - Workload-specific VPCs are logically segmented based on different environments (Dev, Test, Prod, and Tools).
  - Each environment is isolated to cater to specific stages in the software development lifecycle. The Tools account is specific to resources utilized across the other three environments within a project set.

- **Sharing Mechanism via AWS RAM and Its Benefits:**
  - **AWS Resource Access Manager (RAM):**
    - RAM is used to share AWS resources across different accounts, from the Shared Networking account.
    - Workload VPCs in different organizational units (OUs) share common resources via RAM.
    - Resources shared include AWS Transit Gateways, Subnets, AWS License Manager configurations, and Amazon Route 53 Resolver rules.

  - **Benefits:**
    - Reduces operational overhead by eliminating the need to duplicate resources in multiple accounts.
    - Simplifies resource management, making it easier to share and control access to centralized components like Transit Gateways.

- **Configuration Details (CIDR Blocks):**
  - **CIDR Blocks:**
    - Each Workload VPC has a /16 CIDR block.
    - This allows for a large address space within each VPC, accommodating various subnets and future scaling needs.

  - **In the BC Gov ASEA:**
    - Dev VPC: /16
    - Test VPC: /16
    - Prod VPC: /16
    - Tools: /16

  - **Purpose of CIDR Blocks:**
    - CIDR blocks define the IP address range for each VPC, ensuring unique and non-overlapping address spaces.
    - Enables proper addressing and routing within and between the VPCs.


In summary, Workload VPCs are organized by environments (Dev, Test, Prod, Tools), share resources through AWS RAM for centralized management via Shared Networking account, and each VPC is configured with a /16 CIDR block to define its IP address range. This structure and configuration support the secure and scalable hosting of applications across different stages of development and testing in the ASEA.


<!-- - Structure and purpose of workload-specific VPCs (Development, Test, Production)
- Sharing mechanism via AWS RAM and its benefits
- Configuration details like CIDR blocks, each VPC has a /16 CIDR block -->

### Subnets
All subnets within Workload VPCs, including Web, App, and Data, are private, sharing the same IP pool to prevent inter-workload communication across accounts. This subnet configuration ensures a secure and organized environment, with each subnet tailored for distinct purposes within the ASEA infrastructure.
- **Differentiation between Public and Private Subnets:**
  - All subnets in a Workload VPC are designated as private. There is no distinction between public and private subnets within the Workload VPCs.

- **Types of Subnets Created:**
  - Subnets in each Workload VPC are categorized into Web, App, and Data.

- **Use Cases for Each Type of Subnet:**
  - **Web Subnet:**
    - Hosts front-end or client-facing infrastructure.

  - **App Subnet:**
    - Hosts application-tier code, such as EC2 instances and containers.

  - **Data Subnet:**
    - Hosts data-tier code, including RDS instances and ElastiCache instances.

- **Configuration of Subnets in Each Workload VPC:**
  - **CIDR Blocks:**
    - Web Subnet: /20
    - App Subnet: /19
    - Data Subnet: /20

  - **Shared Subnets Between Workloads:**
    - Subnets are shared among all workloads within the same VPC.
    - All workloads in the same VPC/subnet share the same IP address pool.



<!-- - Differentiation between public and private subnets, all subnets in a workload VPC are private
- Subnets created in each workload VPC are Web, App, and Data
- Use cases for each type of subnet
- Configuration of subnets in each workload VPC
  - CIDR blocks
    - Web: /20
    - App: /19
    - Data: /20
  - These subnets are shared between all workloads in a VPC
    - Same IP pool for all workloads in the same VPC/subnet
    - This are security measures to prevent workloads from communicating with each other across accounts -->

### Security groups and NACLs
Security Groups and Network Access Control Lists (NACLs) play distinct roles in ensuring the security of Workload VPCs, with Security Groups acting as instance-level firewalls and NACLs providing an additional layer of defense at the subnet level

- **Difference Between Security Groups and NACLs:**
  - **Security Groups:**
    - Instance-level stateful firewalls controlling inbound and outbound traffic.
    - Rule-based and allow-list focused, supporting ingress/egress based on protocols and sources/destinations.

  - **NACLs:**
    - Stateless constructs operating at the subnet level.
    - Provide an additional layer of security, defining rules for inbound and outbound traffic at the subnet level.

- **Role of NACLs in Subnet Security:**
  - NACLs in ASEA act as a defense-in-depth measure at the subnet level.
  - They are used sparingly, particularly in Data subnets, to enforce segmentation, allowing only necessary inbound traffic from App subnets within the same VPC.

- **ASEA Created and Managed Security Groups and NACLs in Each Workload VPC:**
  <!-- Refine this section -->
  - **Security Groups:**
    - Configured as instance-level firewalls, recommended as the primary data-plane isolation mechanism.
    - Emphasize stateful rules, focusing on allow-listing required ingress traffic.

  - **NACLs:**
    - Used sparingly as a defense-in-depth measure, particularly in Data subnets, to enforce segmentation.
    - ASEA encourages review and tailoring based on specific security requirements.

- **Best Practices for Configuring Security Groups in Workload VPCs:**
  <!-- Refine this section -->
  - Security Groups are recommended as the primary data-plane isolation mechanism.
  - Ingress rules often set to 'allow all' mode (0.0.0.0/0) for ease of operations, with emphasis on consistently allow-listing required ingress traffic.
  - Sample security groups provided as a balance between security, ease of operations, and frictionless development, with the expectation that customers will refine them based on their specific security needs.



<!-- TODO: return to this
- Difference between Security Groups and NACLs in AWS
- Role of NACLs in providing an additional layer of security at the subnet level
- **ASEA created and managed Security Groups and NACLs in each workload VPC**
- **Best practices for configuring Security Groups in workload VPCs**
  - I think this is just not to use NACLs? -->

### Exposing services to the internet

Generally, in the ASEA we recommend one of two methods of exposing services to the internet:
- API Gateway
- Application Load Balancer (ALB)

Making strategic choices between AWS API Gateway and ALBs is essential for optimizing cloud architecture. API Gateway is the preferred option for internet exposure, catering to modern applications using RESTful APIs and serverless computing. ALBs are should only be used for supporting legacy applications, and require integration support from the Public Cloud team. First we'll look at an API Gateway implementation:

<!-- TODO: look at the sample application for specific examples-->
1.  **Create an API in API Gateway:**
  - Navigate to the AWS Management Console and open the API Gateway service.
  - Create a new API, specifying a name and description.
  - Define the necessary resources and methods for your API, and configure the desired authentication mechanisms.
  
2.  **Create a VPC Link:**
  - In the API Gateway console, navigate to the "VPC Links" section.
  - Create a new VPC Link, specifying a name and description.
  - Associate the VPC Link with the VPC that hosts the backend services or resources you want to access.

3.  **Configure Integration with VPC Link:**
  - In the API Gateway console, go to the API's Integration settings.
  - Create a new HTTP integration for the resource/method you want to expose.
  - In the integration settings, choose "VPC Link" as the integration type.
  - Select the previously created VPC Link.

4.  **Deploy the API:**
  - Deploy the API to make it accessible via a public URL.
  - Choose a deployment stage (e.g., production) and deploy your API.

5.  **Access the Internet-Facing Service:**
  - Once deployed, your API is accessible via the provided endpoint URL.
  - Clients can now make requests to this endpoint, and API Gateway will route those requests to the backend services through the VPC Link.

6.  **Secure the API Gateway:**
  - Implement security measures such as API keys, IAM roles, or OAuth2 authentication depending on your requirements.
  - Consider enabling logging and monitoring features for better visibility into API usage.

**Benefits of Using API Gateway and VPC Link:**
- **Security and Isolation:** API Gateway and VPC Link provide a secure and isolated connection between your internet-facing API and backend services within a VPC.
    
- **Scalability:** API Gateway scales automatically to handle varying levels of traffic, ensuring the availability of your internet-facing services.
    
- **Managed Service:** API Gateway is a fully managed service, reducing operational overhead and allowing you to focus on building and improving your APIs.

**When to use ALBs:**
While API Gateway is recommended for modern applications, there is still a use case for ALBs with legacy applications where it may be difficult or impossible to use an API Gateway. If your application is unable to accommodate an API Gateway, then please reach out to the Cloud Pathfinder Team for integration support.



<!-- TODO: return to this
- **Detailed walkthrough of using AWS API Gateway and VPC Link for internet-facing services**
- Guidance on when to use API Gateway vs. ALBs based on application requirements
  - API Gateway is the preferred method for exposing services to the internet
  - ALB is used for legacy applications that cannot be migrated to API Gateway
    - Reach out to the Public Cloud team for assistance with this -->

## Serverless resources

Serverless resources within the AWS Secure Environment Accelerator (ASEA) possess the flexibility to operate within or outside a Virtual Private Cloud (VPC). When deployed within a VPC, these resources are assigned an Elastic Network Interface (ENI), utilizing an IP address from the corresponding subnet's IP pool. Access to VPC resources, including critical databases such as RDS, is exclusive to serverless resources configured **within** the VPC, ensuring a controlled and secure networking environment.



<!-- - Serverless resources can be configured to run in a VPC or not
- When running in a VPC, the serverless resource is assigned an ENI, which consumes an IP address from the subnet's IP pool
- Only VPC-enabled serverless resources can access resources in the VPC such as RDS databases -->

## Appendices
- [AESA network architecture docs](https://aws-samples.github.io/aws-secure-environment-accelerator/latest/architectures/sensitive/network/)
- [GWLB architecture](https://aws-samples.github.io/aws-secure-environment-accelerator/latest/architectures/sensitive/images/perimeter-NFW-GWLB.png)



<!-- - Links to ASEA reference docs, architectures and diagrams -->
