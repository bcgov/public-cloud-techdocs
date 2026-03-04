# Handling EKS Pod IP Exhaustion with Extended Pod Subnets (BCGov LZA)

## Overview

In the B.C. government Landing Zone Accelerator (LZA), workload VPCs often use small primary “app” subnets (for example, `/26`). When you run Amazon EKS with the AWS VPC CNI, **pods use VPC IP addresses** from the same subnet space as worker nodes.

If the subnet is small, you can quickly run out of IP addresses. When this happens, Kubernetes cannot schedule new pods because no IP addresses are available also called **pod IP exhaustion**.

To address this, BCGov LZA supports an **Extended CIDR** approach for EKS pods:

- A secondary CIDR range (for example, `10.10.0.0/16`) can be associated to the workload VPC.
- “Extended” subnets are created from this range with the name prefix `BCGOV-LZA-extended-*`.
- Extended subnets are typically sized `/18` per AZ (for example, one `/18` per Availability Zone).

> **Important:** The extended CIDR is **not routable over the internet**. Route tables are configured so that outbound traffic is **NATed** through a private NAT gateway in the primary (routable) range.  
> This document focuses on the EKS configuration required to use the extended subnets for pods.

## When You need This

You may need extended pod subnets if you notice any of the following:

- Pods stay in `Pending` state and show scheduling errors related to IP availability, for example, "insufficient IP addresses".
- Your EKS clusters run in small subnets (such as `/26`) and pod scaling quickly uses all available IPs
- You increase replicas, add new services, introduce sidecars and pod counts exceed the subnet's practical IP capacity
- The AWS VPC CNI keeps pre-allocated IP "warm pools" on worker nodes, which increases IP pressure even before the pods start

## How it works (EKS view)

Amazon EKS with the **AWS VPC CNI** assigns **VPC-native IPs addresses** to pods. By default:

- Pods and nodes use IP addresses from the **primary node/app subnets**
- As the cluster scales, pods consume more addresses in those subnets, which increase the risk of IP exhaustion

When you enable **VPC CNI custom networking**:

- Nodes stay in the primary subnets (as normal)
- The CNI attaches additional ENIs (pod ENIs) to the node in the **extended subnets**
- Pods receive IP addresses from the extended subnets for example `10.10.x.x` instead of the primary subnet range

To configure this:

1. Create an `ENIConfig` for each Availability Zone (AZ) and point it to the correct extended subnet
2. Enable custom networking in the `vpc-cni` add-on
3. (Recommended) Drain or roll your nodes so new pods consistently receive IPs from the extended range

## Prerequisites

Before you configure EKS, confirm the following:

- You have associated the extended CIDR for example `10.10.0.0/16` to the VPC
- Extended subnets exist in each AZ, and use the prefix `BCGOV-LZA-extended-*` (typically `/18`).
  - Example subnet names:
    - `BCGOV-LZA-extended-app-ca-central-1a`
    - `BCGOV-LZA-extended-app-ca-central-1b`
- Platform networking has already configured routing and NAT for the extended CIDR

If these resources do not exist, request an **Extended CIDR deployment** through your [platform support process](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

## Terraform Example (EKS Custom Networking)

The following example shows how to configure the AWS VPC CNI to allocate **pod IPs from extended subnets**.

### 1 Discover Extended Subnets

```hcl
data "aws_subnets" "pod_subnets" {
  filter {
    name   = "tag:Name"
    values = [
      "BCGOV-LZA-extended-app-ca-central-1a",
      "BCGOV-LZA-extended-app-ca-central-1b"
    ]
  }
}
```

### 2 Create ENIConfig Per Availability Zone

`ENIConfig` tells the VPC CNI which subnet + security group to use when creating **pod ENIs** on nodes in a given AZ.

> **Note:** The `metadata.name` must match the node’s AZ label value when using `topology.kubernetes.io/zone` (for example: `ca-central-1a`, `ca-central-1b`).

```hcl
resource "kubernetes_manifest" "eni_config_a" {
  manifest = {
    apiVersion = "crd.k8s.amazonaws.com/v1alpha1"
    kind       = "ENIConfig"
    metadata   = { name = "ca-central-1a" }
    spec = {
      subnet         = data.aws_subnets.pod_subnets.ids[0]
      securityGroups = [data.aws_security_group.web_sg.id]
    }
  }
}

resource "kubernetes_manifest" "eni_config_b" {
  manifest = {
    apiVersion = "crd.k8s.amazonaws.com/v1alpha1"
    kind       = "ENIConfig"
    metadata   = { name = "ca-central-1b" }
    spec = {
      subnet         = data.aws_subnets.pod_subnets.ids[1]
      securityGroups = [data.aws_security_group.web_sg.id]
    }
  }
}
```

### 3 Configure the VPC CNI Add-on for Custom Networking

This config enables custom networking and selects `ENIConfig` objects based on the node’s zone label.

```hcl
resource "aws_eks_addon" "vpc_cni" {
  cluster_name                = aws_eks_cluster.this.name
  addon_name                  = "vpc-cni"
  resolve_conflicts_on_create = "OVERWRITE"
  resolve_conflicts_on_update = "OVERWRITE"

  configuration_values = jsonencode({
    env = {
      AWS_VPC_K8S_CNI_CUSTOM_NETWORK_CFG = "true"                        # Pods get IPs from ENIs created in ENIConfig subnets
      ENI_CONFIG_LABEL_DEF               = "topology.kubernetes.io/zone" # Select ENIConfig by node zone label (e.g., ca-central-1a)
      AWS_VPC_K8S_CNI_EXTERNALSNAT       = "true"                        # Do not SNAT on the node (platform NAT handles egress)
    }
  })

  depends_on = [aws_eks_cluster.this]
}
```

## Validation

After deployment (and after pods are recreated), check pod IPs using:

```bash
kubectl -n <namespace> get pods -o wide
```

Expected result:

- Pod IPs should be assigned from the extended CIDR range (e.g., `10.10.x.x`).

Optional checks:

- Confirm `ENIConfig` objects exist:

  ```bash
  kubectl get eniconfig
  kubectl get eniconfig ca-central-1a -o yaml
  kubectl get eniconfig ca-central-1b -o yaml
  ```

- Confirm the VPC CNI environment variables are set on `aws-node`:

  ```bash
  kubectl -n kube-system get ds aws-node -o jsonpath='{range .spec.template.spec.containers[?(@.name=="aws-node")].env[*]}{.name}={.value}{"\n"}{end}' \
    | egrep 'AWS_VPC_K8S_CNI_CUSTOM_NETWORK_CFG|ENI_CONFIG_LABEL_DEF|AWS_VPC_K8S_CNI_EXTERNALSNAT'
  ```

## Operational notes

### Mixed pod IP ranges during transition

After enabling custom networking, you may briefly see pods with both:

- Primary range IPs (legacy allocations)
- Extended range IPs (new allocations)

This is expected due to pre-allocated IP “warm pools” on existing nodes.

**Recommendation:** Roll or drain worker nodes after enabling custom networking so that new pod allocations consistently use extended subnets.

### Security groups

The security group you define in `ENIConfig` applies to the pod ENIs. Make sure this security group allows the required inbound and outbound traffic for your workload. For example:

- Allow traffic from the load balancer to pod ports
- Allow outbound traffic to required dependencies

In most deployments, you should use a **dedicated security group for ENIs**. Explicitly allow traffic from the load balancer security group to the required pod ports.

## Summary

This approach resolves pod IP exhaustion by:

- Keeping worker nodes in the primary, routable subnets
- Allocating pod IPs from large extended subnets using AWS VPC CNI custom networking

If you run out of IP addresses in `/26` app subnets, request an Extended CIDR deployment and apply the configuration in this guide.
