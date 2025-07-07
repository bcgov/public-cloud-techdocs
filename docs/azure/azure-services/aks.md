# Azure Kubernetes Service (AKS)

Last updated: **{{ git_revision_date_localized }}**

When deploying AKS clusters, it is critical to ensure that the **service and pod CIDR ranges do not overlap** with any other networks that cluster pods will need to communicate with. Overlapping address spaces can cause connectivity issues between your AKS workloads and other resources, both within Azure and on-premises.

!!! warning "Important notes about AKS networking"
    - **Virtual Networks (VNets) are pre-created** by the Public cloud team. You cannot create new VNets, but you can create subnets within the existing VNet for your AKS resources
    - Plan your subnet, pod, and service address ranges carefully to avoid conflicts and to allow for future scaling
    - **Reserved address space**: The `10.10.0.0/16` range is reserved for AKS deployments. However, this same range may also be used for extended non-routable peered VNets if teams require additional IP addresses beyond what's provided in the standard routable VNet
    - **Future network expansion**: If you anticipate needing an extended network in the future, it's important to plan your AKS CIDR ranges accordingly to avoid conflicts. Contact the Public Cloud team early in your planning process for guidance on optimal address allocation

## DNS

In AKS, CoreDNS by default forwards all external queries to the Azure-provided DNS endpoint at `168.63.129.16` unless you’ve overridden the VNet DNS settings. In our Landing Zones, we have configured the VNet DNS servers to point to the Azure Firewall DNS proxy.

When your AKS nodes and pods use Azure CNI, they inherit DNS servers from the virtual network. By default, AKS **does not** enable Azure CNI Overlay. Azure CNI Overlay is only applied when you pass both `--network-plugin azure` and `--network-plugin-mode overlay` during deployment.

!!! tip "Recommendation"
    We strongly recommend using Azure CNI Overlay for AKS clusters to ensure proper DNS resolution and network performance. If you are experiencing issues with DNS resolution in your AKS cluster, it may be due to the absence of Azure CNI Overlay.

If you encounter DNS failures — such as `cert-manager` hanging on ACME validation despite CoreDNS being healthy — you can override the nameservers in the `cert-manager` pod spec.

```yaml
spec:
  template:
    spec:
      dnsConfig:
        nameservers:
          - 10.53.244.4 # Azure Firewall DNS proxy
```

For more information and best practices, see:

- [AKS Networking Concepts](https://learn.microsoft.com/en-us/azure/aks/concepts-network)
- [IP Address Planning for AKS](https://learn.microsoft.com/en-us/azure/aks/concepts-network-ip-address-planning)
- [CNI Overview](https://learn.microsoft.com/en-us/azure/aks/concepts-network-cni-overview)
- [Azure CNI Overlay](https://learn.microsoft.com/en-us/azure/aks/concepts-network-azure-cni-overlay)
- [Azure CNI Pod Subnet](https://learn.microsoft.com/en-us/azure/aks/concepts-network-azure-cni-pod-subnet)

> **Need help or unsure about your AKS networking setup?**
> Reach out to the Public Cloud team for advice via [Jira Service Management (JSM)](https://citz-do.atlassian.net/servicedesk/customer/portal/3) or [Rocket.Chat](https://chat.developer.gov.bc.ca/). See [Support options](../../welcome/support.md) for more details.
