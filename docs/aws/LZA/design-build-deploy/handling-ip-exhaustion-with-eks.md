# Handling EKS Pod IP Exhaustion with Extended Pod Subnets (BCGov LZA)

## Overview

In the BCGov Landing Zone Accelerator (LZA), workload VPCs commonly use small primary “app” subnets (for example, `/26`). When running Amazon EKS with the AWS VPC CNI, **pods consume VPC IP addresses** from the same subnet space as worker nodes. In environments with small subnets, this can lead to **pod IP exhaustion** (pods fail to schedule due to lack of available IPs).

To address this, BCGov LZA supports an **Extended CIDR** approach for EKS pods:

- A secondary CIDR range (for example, `10.10.0.0/16`) can be associated to the workload VPC.
- “Extended” subnets are created from this range with the name prefix `BCGOV-LZA-extended-*`.
- Extended subnets are typically sized `/18` per AZ (for example, one `/18` per Availability Zone).

> **Important:** The extended CIDR is **not routable over the internet**. Route tables are configured so that outbound traffic is **NATed** through a private NAT gateway in the primary (routable) range.  
> This document focuses on the EKS configuration required to use the extended subnets for pods.

## When You Need This

You may need extended pod subnets if you observe:

- Pods stuck in `Pending` state with scheduling failures related to IP availability (for example, messages indicating insufficient IP addresses).
- EKS clusters deployed into small subnets (such as `/26`) where pod scaling quickly consumes available IPs.
- Growth in workload replicas, new services, or sidecars causing pod counts to rise beyond the subnet’s practical IP capacity.
- IP pressure caused by the AWS VPC CNI maintaining pre-allocated IP “warm pools” on worker nodes (to speed up pod launches).

## How It Works (EKS View)

Amazon EKS with the **AWS VPC CNI** assigns pods **VPC-native IPs**. By default:

- Pods and nodes draw addresses from the **primary node/app subnets**.
- As the cluster scales, pods consume a significant portion of the available addresses in these subnets, increasing the risk of exhaustion.

With **VPC CNI Custom Networking** enabled:

- Nodes remain in the primary subnets (as normal).
- The CNI attaches additional ENIs (“pod ENIs”) to the node in the **extended subnets**.
- Pods receive IPs from the extended subnets (e.g., `10.10.x.x`) rather than the primary subnet range.

This is accomplished by:

1. Creating an `ENIConfig` per AZ that points to the correct extended subnet.
2. Enabling custom networking in the `vpc-cni` add-on.
3. (Recommended) Rolling/draining nodes so new pod allocations consistently use the extended range.

## Prerequisites

Before configuring EKS:

- The Extended CIDR (e.g., `10.10.0.0/16`) has been associated to the VPC.
- Extended subnets exist in each AZ, named with the prefix `BCGOV-LZA-extended-*` (typically `/18`).
  - Example names:
    - `BCGOV-LZA-extended-app-ca-central-1a`
    - `BCGOV-LZA-extended-app-ca-central-1b`
- Routing/NAT configuration for the extended CIDR is already in place (handled by platform networking).

If you do not have these resources, request an **Extended CIDR deployment** through your [platform support process](https://citz-do.atlassian.net/servicedesk/customer/portal/3).

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

## Operational Notes

### Mixed Pod IP Ranges During Transition

After enabling custom networking, you may briefly see pods with both:

- primary range IPs (legacy allocations)
- extended range IPs (new allocations)

This is expected due to pre-allocated IP “warm pools” on existing nodes.

**Recommendation:** Roll or drain worker nodes after enabling custom networking so that new pod allocations consistently use extended subnets.

### Security Groups

The security group specified in `ENIConfig` applies to the pod ENIs. Ensure it allows required inbound/outbound traffic for your workload (for example, load balancer to pod ports, and egress to required dependencies). In many deployments, it is recommended to use a dedicated pod ENI security group and explicitly allow traffic from the load balancer security group to the pod ports.

## Summary

This approach resolves pod IP exhaustion by:

- keeping worker nodes in the primary (routable) subnets, and
- allocating pod IPs from large, extended subnets using AWS VPC CNI Custom Networking.

If you are experiencing IP constraints in `/26` app subnets, request an Extended CIDR deployment and apply the configuration in this guide.
