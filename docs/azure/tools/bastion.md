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

!!! tip "Cost savings options"
    As a suggestion to save costs, if you only need to access Virtual Machines (via Bastion) within a specific time frame (ie. between 8:00 AM to 5:00 PM), you can create a scheduled pipeline to delete the Bastion deployment at the end of the day and recreate it in the morning. This way, you only pay for the service when you need it.

    The following article is an example of this, although it uses Azure Bicep and Deployment Stacks. But the concepts can be applied to Terraform as well: [Save Cost with Azure Bicep Deployment Stacks](https://hungryboysl.wordpress.com/2025/02/02/save-cost-with-azure-bicep-deployment-stackswt-mc_idaz-mvp-5005246/).

    If you are using the [Bastion sample code](https://github.com/bcgov/azure-lz-samples/blob/main/tools/bastion/README.md) provided, please be aware that the sample code also includes _creating_ the **AzureBastionSubnet**. Therefore, you may need to adjust the sample code to not delete the subnet when the Bastion is deleted, or ensure you provide an available address space when it recreates the subnet along with the Bastion deployment.
