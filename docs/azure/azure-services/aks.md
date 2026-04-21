# Azure Kubernetes Service (AKS)

Last updated: **{{ git_revision_date_localized }}**

AKS networking and security choices affect cluster reliability. Use this page to plan CIDR ranges, DNS, and core security settings.

---

## Networking

When you deploy AKS clusters, keep service and pod CIDR ranges unique. Do not overlap them with connected networks. Overlap can break connectivity between AKS workloads, Azure resources, and on-premises systems.

!!! warning "Important notes about AKS networking"
    - **Public Cloud team creates VNets**: Use the existing VNets for AKS. Create subnets in those VNets for AKS resources.
    - **Plan address ranges early**: Choose subnet, pod, and service ranges that avoid conflicts and support growth.
    - **Reserved pod CIDR ranges**: `10.10.0.0/18` and `10.10.128.0/18` are reserved for AKS pod CIDRs. Each block provides about 16,384 IP addresses.
    - **Reserved service CIDR ranges**: `10.10.64.0/22` and `10.10.192.0/22` are reserved for AKS service CIDRs. Each block provides about 1,024 IP addresses.
    - **Plan for future expansion**: If you need extended networking later, plan AKS CIDR ranges now. Contact the Public Cloud team early for address planning support.

> **Need help or unsure about your AKS networking setup?**
> Contact the Public Cloud team through [Jira Service Management (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3). See [Support options](../../welcome/support.md) for more details.

## DNS

In AKS, CoreDNS forwards external queries to `168.63.129.16` by default. This changes only when you override VNet DNS settings. In Landing Zones, VNet DNS servers point to the Azure Firewall DNS proxy.

When AKS nodes and pods use Azure CNI, they inherit DNS servers from the virtual network. AKS does not enable Azure CNI Overlay by default. It applies only when you pass `--network-plugin azure` and `--network-plugin-mode overlay`.

!!! tip "Recommendation"
    Use Azure CNI Overlay for AKS clusters. It helps DNS resolution and network performance.

If DNS fails, `cert-manager` can stall on ACME validation even when CoreDNS is healthy. You can override nameservers in the `cert-manager` pod spec.

```yaml
spec:
  template:
    spec:
      dnsConfig:
        nameservers:
          - 10.53.244.4 # Azure Firewall DNS proxy
```

## Security recommendations

Use these AKS settings to improve cluster security. The following settings are **required** in Landing Zones.

- **Use Azure CNI Overlay**: Improve network isolation and reduce IP conflict risk.
- **Enable Entra ID authentication**: Use centralized identities to control cluster access.
- **Disable local accounts**: Reduce unauthorized access risk and require Entra ID sign-in.
- **Use Workload Identity and OIDC for pods**: Let workloads access Azure resources without stored secrets.
- **Enable Key Vault and Azure Policy integration**: Secure secret handling and enforce policy controls.
- **Enable Image Cleaner**: Remove unused images and reduce attack surface.
- **Enable Advanced Container Networking Services (ACNS)**: Apply stronger network controls, including policies and micro-segmentation.
- **Enable Cilium dataplane**: Improve policy enforcement and network visibility.

## Best practices and further reading

For more information and best practices, see:

- [AKS Networking Concepts](https://learn.microsoft.com/en-us/azure/aks/concepts-network)
- [IP Address Planning for AKS](https://learn.microsoft.com/en-us/azure/aks/concepts-network-ip-address-planning)
- [CNI Overview](https://learn.microsoft.com/en-us/azure/aks/concepts-network-cni-overview)
- [Azure CNI Overlay](https://learn.microsoft.com/en-us/azure/aks/concepts-network-azure-cni-overlay)
- [Azure CNI Pod Subnet](https://learn.microsoft.com/en-us/azure/aks/concepts-network-azure-cni-pod-subnet)

!!! info "Recommended reading"
    If you plan to deploy AKS in a Landing Zone, this book can help. It shares production lessons and calls out decisions that require a cluster rebuild.

    - [The AKS Book: The Real-World Guide to Azure Kubernetes Service](https://www.amazon.ca/dp/B0GNNVSG67)

    Example insights:

    - **Use Azure CNI Overlay with Cilium**: Improve packet processing, policy enforcement, service routing, and traffic visibility.
    - **Plan CIDR ranges before deployment**: You cannot change pod CIDR, service CIDR, or DNS service IP after cluster creation. Overlap with external networks can force a full cluster rebuild.
    - **Use availability zones (1, 2, 3)**: Apply this to system and user node pools in all environments.
