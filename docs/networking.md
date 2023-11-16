# Networking

## Introduction

- Objectives of the document
- Importance of networking in cloud development and operations

## Network Architecture

- High-level overview of the ASEA network architecture
- network architecture diagram is available in sharepoint
- link to the AESA network architecture docs (We use the GWLB architecture with third-party firewalls)

## Gateway Load Balancers and CheckPoint Firewalls

- Architecture and deployment of Gateway Load Balancers in the Perimeter Account
- Integration of CheckPoint Firewalls with Gateway Load Balancers
- Only HTTP/HTTPS traffic is routed through the firewalls
  - This means that SSH egress traffic is blocked which can cause issues with accessing git repositories and other services that use SSH.
    - To access git repositories, use HTTPS instead of SSH

## Transit Gateway

- Role of Transit Gateway in ASEA LZ networking
- Configuration details: attachment to VPCs, route tables, and transit gateway policies
- How Transit Gateway facilitates inter-VPC communication and connectivity to on-premises networks

## Workload VPCs

- Structure and purpose of workload-specific VPCs (Development, Test, Production)
- Sharing mechanism via AWS RAM and its benefits
- Configuration details like CIDR blocks, each VPC has a /16 CIDR block

### Subnets

- Differentiation between public and private subnets, all subnets in a workload VPC are private
- Subnets created in each workload VPC are Web, App, and Data
- Use cases for each type of subnet
- Configuration of subnets in each workload VPC
  - CIDR blocks
    - Web: /20
    - App: /19
    - Data: /20
  - These subnets are shared between all workloads in a VPC
    - Same IP pool for all workloads in the same VPC/subnet
    - This are security measures to prevent workloads from communicating with each other across accounts

### Security Groups and Network ACLs

- Difference between Security Groups and NACLs in AWS
- Role of NACLs in providing an additional layer of security at the subnet level
- ASEA created and managed Security Groups and NACLs in each workload VPC
- Best practices for configuring Security Groups in workload VPCs

### Exposing Services to the Internet

- Detailed walkthrough of using AWS API Gateway and VPC Link for internet-facing services
- Guidance on when to use API Gateway vs. ALBs based on application requirements
  - API Gateway is the preferred method for exposing services to the internet
  - ALB is used for legacy applications that cannot be migrated to API Gateway
    - Reach out to the Public Cloud team for assistance with this

## Serverless Resources

- Serverless resources can be configured to run in a VPC or not
- When running in a VPC, the serverless resource is assigned an ENI, which consumes an IP address from the subnet's IP pool
- Only VPC-enabled serverless resources can access resources in the VPC such as RDS databases

## Appendices

- Links to ASEA reference docs, architectures and diagrams
