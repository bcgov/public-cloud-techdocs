# Azure Container Instances

Last updated: **{{ git_revision_date_localized }}**

If you use [Azure Container Instances](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-overview), and you plan to [integrate it with an Azure Virtual Network](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-vnet), keep in mind this limitation: **The containers will not inherit the VNet custom DNS resolvers. By default, they will use the Azure DNS service with the virtual IP address of 168.63.129.16**.

To specify the custom DNS servers, you need to use a YAML file for the deployment. The following is an example of the content of such a file:

```yaml
apiVersion: '2025-09-01'
location: canadacentral
name: aci-vnet-dns
properties:
  containers:
  - name: mycontainer
    properties:
    ...
  dnsConfig:
    nameServers:
    - 10.0.0.10 # DNS Server 1
    - 10.0.0.11 # DNS Server 2
  ipAddress:
    type: Private
    ports:
    - port: 80
  subnetIds:
    - id: /subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>>
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Please refer to the Microsoft [Deploy a container group with custom DNS settings](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-custom-dns#prerequisites) documentation for additional guidance.
