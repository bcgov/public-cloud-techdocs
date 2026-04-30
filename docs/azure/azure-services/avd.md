# Azure Virtual Desktop (AVD)

Last updated: **{{ git_revision_date_localized }}**

Azure Virtual Desktop (AVD) is a desktop and app virtualization service that runs on Azure.
It lets users access virtual desktops and applications from anywhere on any device.
AVD supports remote work with secure, scalable access and centralized IT control.

---

## High-level end-user connectivity flow

1. User signs in to the AVD client
   - They open the Windows Desktop Client, Web Client, or Remote Desktop app.
   - They authenticate with Entra ID.
2. AVD queries the user’s Workspaces
   - A **Workspace** is just a container that groups application groups for the user.
     - The client asks AVD: "**Which Workspaces does this user have access to?**"
     - AVD returns the list of Workspaces the user is subscribed to.
3. Workspace returns the user’s Application Groups
   - Inside each Workspace are **Application Groups**, which define what the user can launch:
     - **Desktop Application Group (DAG)** → full desktop
     - **RemoteApp Application Group (RAG)** → individual apps
   - The Workspace tells the client: "**Here are the desktops and apps this user is allowed to see.**"
4. Application Group maps the user to a Host Pool
   - Each Application Group is linked to **one Host Pool**.
   - The DAG/RAG says: "**This user belongs to this Host Pool.**"
   - This is the key relationship: `Workspace -> Application Group -> Host Pool -> Session Hosts`
5. Host Pool selects a Session Host
   - The Host Pool decides which _**VM**_ the user should land on:
     - **Breadth-first** → spread users evenly
     - **Depth-first** → fill one VM before moving to the next
     - **Persistent** (personal) → always the same VM
   - The Host Pool returns the chosen session host to the AVD broker.
6. User connects through the AVD control plane
   - The user **never connects directly** to the VM’s IP.
   - Instead:
     - The AVD broker establishes a **reverse connection** from the VM to the user.
     - Traffic flows through **AVD’s secure control plane**, not your VNet.
   - This is why AVD works even without public IPs.
7. User receives their desktop or app
   - The session host VM accepts the connection and starts the user session.

## Networking

Your network design depends on where end users connect from.
Most teams use one of these two AVD network architectures:

1. **AVD with public endpoints**: AVD resources run in a VNet and use public endpoints for feed discovery and session access. Users can connect from anywhere without the B.C. government network.
2. **AVD with private endpoints**: AVD resources run in a VNet and use [private link](https://learn.microsoft.com/en-us/azure/virtual-desktop/private-link-overview) for host pool and session host connectivity. Users connect through the B.C. government network, either on-premises or through VPN.

!!! warning "Public Endpoint Security Policies"
    If you plan to deploy AVD with public endpoints, submit a [support request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team.
    Security policies block public endpoint deployment for AVD host pools and workspaces by default.

!!! example "Deploying AVD in a Landing Zone"
    Microsoft provides a [QuickStart template](https://learn.microsoft.com/en-us/azure/virtual-desktop/quickstart?tabs=windows) for AVD deployment.

    The template deploys some resources that already exist in Azure Landing Zones, such as the virtual network. It also omits supporting resources that most production deployments need, such as Key Vault, Log Analytics, FSLogix storage accounts, and scaling plans.

    We created a Terraform module to support Azure Virtual Desktop deployments. It deploys AVD resources, including host pools, session hosts, application groups, workspaces, scaling plans, and FSLogix storage accounts. It also deploys supporting resources such as Key Vault and Log Analytics with diagnostics enabled.

    The module deploys into an existing virtual network, such as one from Azure Landing Zones, and creates the required subnets and network security groups for AVD.

    You can find this module in the [Azure Landing Zone Samples](https://github.com/bcgov/azure-lz-samples) repository under `/applications/azure_virtual_desktop/`. Review the module [README](https://github.com/bcgov/azure-lz-samples/blob/main/applications/azure_virtual_desktop/README.md) for setup instructions.

## Best practices and further reading

For more information and best practices, see:

- [Prerequisites for Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/prerequisites?tabs=portal)
- [Azure Virtual Desktop service architecture and resilience](https://learn.microsoft.com/en-us/azure/virtual-desktop/service-architecture-resilience)
- [Security recommendations for Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/security-recommendations)
- [Understanding Azure Virtual Desktop network connectivity](https://learn.microsoft.com/en-us/azure/virtual-desktop/network-connectivity)
- [Apply Zero Trust principles to an Azure Virtual Desktop deployment](https://learn.microsoft.com/en-us/security/zero-trust/azure-infrastructure-avd?context=/azure/virtual-desktop/context/context)
