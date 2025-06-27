# Networking in AWS Landing Zone Accelerator (LZA)

Last updated: **{{ git_revision_date_localized }}**

## Overview

AWS Landing Zone Accelerator (LZA) uses a **hub-and-spoke architecture** where each workload VPC has a dedicated CIDR range. This design provides clear network isolation and enables easy identification of traffic sources - particularly useful for on-premises firewall rules when using Direct Connect.

!!! info "Network Isolation"
    All VPCs in LZA are completely network-isolated from each other. Dev, Test, Prod, and Tools accounts cannot communicate privately - all cross-account communication must use AWS APIs or public endpoints.
    
    If you have a specific use case requiring VPC-to-VPC communication, please contact the [Public Cloud Service Desk (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to discuss your requirements.

## Architecture

LZA implements a hub-and-spoke topology with:

- **Hub**: Transit Gateway serves as the central routing hub for connectivity
- **Spokes**: Individual workload VPCs with dedicated CIDR ranges
- **Complete isolation**: No private communication between VPCs
- **Dedicated CIDRs**: Each VPC gets unique IP ranges for traffic identification

## VPC and Subnet Structure

### VPC Limitations

Each workload receives a **/24 VPC** providing **256 IP addresses**.

### Subnet Allocation

Each VPC is divided into **8 subnets across 2 Availability Zones (A and B)**:

| Subnet | CIDR | Total IPs (per AZ) | Usable IPs (per AZ) | Total Across Both AZs |
|--------|------|-------------------|--------------------|--------------------|
| Web-MainTgwAttach | /27 | 32 | 27 | 54 usable |
| App | /26 | 64 | 59 | 118 usable |
| Data | /28 | 16 | 11 | 22 usable |
| Management | /28 | 16 | 11 | 22 usable |

!!! warning "IP Address Limitations"
    With only 256 IPs per VPC total, each subnet type exists in both AZ-A and AZ-B. Plan your IP usage carefully considering the per-AZ allocation.

### Subnet Use Cases

Each subnet type is designed for specific workload categories:

**Web-MainTgwAttach subnets**
- Application Load Balancers (ALBs) for public and internal routing

**App subnets**
- EC2 instances running application servers
- ECS/Fargate containers and services
- Lambda functions with VPC configuration
- Application-tier compute resources

**Data subnets**  
- RDS database instances
- ElastiCache clusters
- EFS mount targets
- Data storage and persistence services

**Management subnets**
- Administrative tools and utilities

!!! note "Security Group and Network ACL Strategy"
    LZA includes pre-configured security groups and Network ACLs that implement the principle of least privilege, allowing traffic flow from Web → App → Data subnets.
    
## Extended Network - IP Exhaustion Solution

### Secondary CIDR Range

When IP addresses are exhausted, a **secondary non-routable CIDR range** (`10.10.0.0/16`) can be added upon request:

- **Extended subnets**: Additional App and Data subnets in the secondary range
- **NAT routing**: Traffic from non-routable range routes through Private NAT in the routable range
- **Request process**: Contact the [Public Cloud Service Desk (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to add secondary CIDR ranges

## Exposing Services Publicly

### Recommended Methods

To make workloads publicly available, use:

1. **API Gateway** (preferred where appropriate) - Can be public and linked to private VPC with VPC Link
2. **Application Load Balancer (ALB)** - Requires routing through public ALBs

### Application Load Balancer Public Routing

**Public routing to internal ALBs** is now **self-serve** through a two-tier architecture:

#### How Public Routing Works

1. **Public ALB** (Perimeter Network) receives internet traffic on `*.{license_plate}.stratus.cloud.gov.bc.ca`
2. **Automatic routing** from public ALB to your tagged internal ALB
3. **Internal ALB** (Your VPC) uses rules (host, path) to route traffic to target groups

#### Configuration Requirements

- **Tag for public routing**: Tag Internal ALBs with `Public = True` to trigger automation
- **443 listener required**: Teams **must** use the 443 listener on their internal ALB to ensure all internal traffic outside their VPC is encrypted in transit
- **Health check requirement**: Add a path-based rule returning 200 response on `/bcgovhealthcheck`
- **TLS certificate**: Each workload includes a default certificate for `internal.stratus.cloud.gov.bc.ca`, or teams can create and use their own certificates
- **Limitation**: Currently only **one internal ALB** per workload supports public routing

#### Traffic Flow

```
Internet → Public ALB (Perimeter) → Internal ALB (Your VPC) → Target Groups
         *.{license_plate}.stratus.cloud.gov.bc.ca    (443 encrypted)
```

!!! warning "Encryption Requirement"
    The 443 listener on your internal ALB is mandatory to ensure all traffic between the public ALB and your internal ALB is encrypted in transit.

!!! tip "SSL/TLS Certificate Options"
    You can either use the provided default certificate for `internal.stratus.cloud.gov.bc.ca` to quickly get started, or create and use your own custom certificates for your specific domain requirements.

## Platform Constraints

### Managed Infrastructure

- **VPCs and subnets**: Platform-managed, cannot be created or modified
- **Security groups**: Users can create new security groups, but platform-created security groups with Accelerator tags cannot be modified
- **HTTPS requirement**: Only HTTPS applications supported for public endpoints

### Network Connectivity

- **No VPC-to-VPC communication**: All VPCs are completely isolated
- **Cross-account data sharing**: Must use AWS APIs, public endpoints, or external services
- **On-premises connectivity**: Direct Connect available (coming soon)

## Best Practices

1. **Plan IP usage** carefully given the /24 VPC limitation
2. **Use security groups** for traffic control between resources
3. **Design for isolation** - architect applications assuming no private cross-account communication
4. **Request secondary CIDR** early if you anticipate IP exhaustion

## Related Documentation

- [Requirements](requirements.md) - Platform limitations and constraints
- [Be Mindful](../best-practices/be-mindful.md) - Important considerations for LZA
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [AWS Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [AWS API Gateway](https://docs.aws.amazon.com/apigateway/)
