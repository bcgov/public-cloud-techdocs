# Azure Landing Zone sample code

Last updated: **{{ git_revision_date_localized }}**

We have a centralized repository ([Azure Landing Zone Samples](https://github.com/bcgov/azure-lz-samples)) of sample code that you can use to get started. This repository contains sample code for application patterns and tools for use in the OCIO Azure Landing Zone.

!!! note "Sample code"
    The samples provided in the `azure-lz-samples` repository are provided as-is. They are intended to be used as a reference and starting point, but may require additional modification to work in your environment.

!!! question "Missing a sample?"
    Don't see an example that fits your needs? Have an idea for a new sample? Feel free to [open an issue](https://github.com/bcgov/azure-lz-samples/issues), or [submit a pull request](https://github.com/bcgov/azure-lz-samples/pulls) in the `azure-lz-samples` repository. We welcome contributions and suggestions for new samples.

## Sample applications
<!-- TODO: Update these links once the sample app code is migrated to the new repo -->
- Samples pending

For additional guidance on application architecture, please refer to the [Microsoft Architecture Center](https://docs.microsoft.com/en-us/azure/architecture/).

!!! warning "Microsoft QuickStart templates"
    Many of the Microsoft QuickStart templates are not designed to be deployed in a regulated/government environment. Most templates will attempt to deploy resources that are not allowed in the Azure Landing Zone (ie. Private DNS Zones, etc.). 
    
    It is highly likely that you will need to modify the templates to work in a regulated/government environment. Please expect additional effort when attempting to use these templates.

## Sample tools

- Azure Cloud Shell in a Virtual Network: [Azure Cloud Shell in a Virtual Network](https://github.com/bcgov/azure-lz-samples/blob/main/tools/cloud_shell_vnet/README.md)
- Azure Bastion: [Azure Bastion](https://github.com/bcgov/azure-lz-samples/blob/main/tools/bastion/README.md)
- CI/CD self-hosted agents: [CI/CD Self-Hosted GitHub Runners](https://github.com/bcgov/azure-lz-samples/blob/main/tools/cicd_self_hosted_agents/README.md)
- Managed DevOps Pools: [CI/CD Managed DevOps Pools](https://github.com/bcgov/azure-lz-samples/blob/main/tools/cicd_managed_devops_pools/README.md)

!!! tip "Other tools"
    Also see further tools and details on the [Tools](../tools/tools.md) page.
