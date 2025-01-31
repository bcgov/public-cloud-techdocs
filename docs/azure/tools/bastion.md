# Azure Bastion

Last updated: **{{ git_revision_date_localized }}**

Azure Bastion is a fully managed PaaS service that you provision to securely connect to virtual machines via private IP address. It provides secure and seamless RDP/SSH connectivity to your virtual machines directly over TLS from the Azure portal.

Azure Bastion requires a Virtual Network, and a specifically named subnet called `AzureBastionSubnet`. There are also specific Network Security Group (NSG) rules that need to be configured to allow traffic to and from the Azure Bastion service.

!!! warning "Bastion Subnet Size"
    Please review the Microsoft documentation about the requirements for the [Azure Bastion subnet](https://learn.microsoft.com/en-us/azure/bastion/configuration-settings#subnet), in particular the subnet size.

!!! tip "Azure Bastion Deployment"
    Microsoft provides multiple [deployment guides](https://learn.microsoft.com/en-us/azure/bastion/tutorial-create-host-portal) for Azure Bastion.

    To support our customers, and expedite the deployment of all the required resources, we have created a Terraform module that deploys the required Subnet, the Network Security Group (with all the required rules), and the Bastion host (with a "**Basic**" SKU, as the "**Developer**" SKU is not yet available in the Canada regions).
    
    You can find this module in the [Azure Landing Zone Samples](https://github.com/bcgov/azure-lz-samples) repository, under `/tools/bastion/`. Please ensure to review the [README](https://github.com/bcgov/azure-lz-samples/blob/main/tools/bastion/README.md) file in the module for instructions on how to use it.
