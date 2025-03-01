# Azure landing zone guardrails: What you need to know

Last updated: **{{ git_revision_date_localized }}**

This document explains the "guardrails" (security and compliance rules) set up in our Azure Landing Zone. These guardrails are implemented using Azure Policy, and they are in place to ensure our cloud environment remains secure, compliant (including with standards like **CCCS Medium [Canada Federal PBMM]**), and cost-effective. They help us maintain consistency and prevent configurations that could lead to problems.

Think of these guardrails as automatic checks and balances. They don't require you to do anything extra most of the time, but they might prevent you from doing something that goes against our established best practices, or they might automatically configure resources to meet compliance standards.

!!! info "About Azure Policy Effects"
    This document focuses on the things that you might be *prevented* from doing (Deny policies), the things you *must* do as a result, or things that will be automatically configured (DeployIfNotExists and Modify policies). Audit policies are also in place, which will flag non-compliant resources, but won't block deployments.

## Key areas covered

1.  [**Resource Deployment Restrictions:**](#resource-deployment-restrictions) Where and what you can deploy.
2.  [**Networking Restrictions:**](#networking-restrictions) How your networks are configured and protected.
3.  [**Security Requirements:**](#security-requirements) Mandatory security settings.
4.  [**Data Protection:**](#data-protection) How your data is protected at rest and in transit.
5.  [**Cost Optimization:**](#cost-optimization) Preventing unnecessary costs.
6.  [**Identity & Access Management:**](#identity--access-management) Controlling who can access resources.
7.  [**Specific Service Guardrails:**](#specific-service-guardrails) Restrictions related to specific Azure services.
8.  [**Monitoring:**](#monitoring) Diagnostic data collection and automated security checks.
9.  [**Tagging:**](#tagging) Automatic application of tags.

## Resource deployment restrictions

*   **Allowed Locations (Regions):** Resource Groups and resources can only be deployed in Canada Central and Canada East. If you try to deploy to a region not on the approved list, the deployment will be **blocked**. Note that networking is only provided in Canada Central.
*   **Resource Groups:** Resource Groups can only be created in Canada Central and Canada East regions. Resources must be deployed to a Resource Group in the same region as the resource itself. For example, if you want to deploy a VM in Canada Central, you must first create a Resource Group in Canada Central and then deploy the VM into that Resource Group.
*   **Denied Resource Types:** There is a "Deny" policy for specific resource types that are *not* allowed within the landing zone, preventing their creation.

## Networking restrictions

### General network security
*   **No Public IPs (Generally):** You *cannot* create public IP addresses directly. This is a crucial security measure to minimize our public attack surface. Services should typically be accessed through private endpoints or other secure means.
*   **No Public IP on NICs:** Network interfaces *cannot* have a public IP address associated.
*   **No Management Port Access from Internet:** NSG rules allowing inbound access from the "Internet" source on common management ports (SSH/RDP - ports 22 and 3389) are *blocked*. Access to these ports is only allowed through secure, managed pathways (e.g., Azure Bastion, Just-In-Time VM Access).

### Subnet configuration requirements
*   **Subnets Require Network Security Groups (NSGs):** Every subnet *must* have a Network Security Group (NSG) associated with it. You *cannot* create a subnet without an NSG. NSGs act like mini-firewalls for your subnets, controlling inbound and outbound traffic.
*   **Subnets Require Private Endpoint Network Policies**: Subnets created must have the private endpoint network policy enabled.
*   **No Service Endpoints:** Service Endpoints are *denied*. The preferred approach is to use Private Endpoints for secure access to PaaS services.
*   **Private Subnets Only:** All subnets are configured as private by default, with no direct outbound internet access. Any required internet connectivity is managed centrally through the central virtual WAN and firewall infrastructure. This aligns with Microsoft's move away from default outbound access in virtual networks.

### VNet management
*   **No VNet Creation:** New VNets *cannot* be created in the landing zone.
*   **No DNS Changes to VNet:** VNet DNS settings are enforced and cannot be changed. This is configured at the landing zone level.
*   **VNet Peering Not Allowed:** VNet peering is not allowed within the landing zone. All network connectivity is managed centrally.

### Protected resources
*   **Protected Networking Resources Denied:** Users cannot create Express Route circuits, VPN Sites, VPN/NAT/Local Gateways, or Route Tables.

### Application gateway requirements

*   Application Gateways *must* use the "WAF_v2" SKU (Web Application Firewall).
*   The WAF *must* be in "Prevention" mode.
*   TLS 1.2 must be used

### Private endpoints and DNS integration

*   **Private Endpoints Required:** Access to most Azure PaaS services (like Storage Accounts, Key Vault, SQL Databases, etc.) is restricted to private endpoints only. This means that these services will not have public IP addresses and will only be accessible from within your virtual network (or peered networks). Creating a PaaS service without configuring a private endpoint, or attempting to enable public access after creation, will be blocked by a "Deny" policy.
*   **Centralized Private DNS Zones:** To make Private Endpoints work correctly, DNS resolution needs to be handled properly. The landing zone uses a centralized set of Private DNS Zones, managed within the "Connectivity" subscription (or a dedicated DNS subscription).
    *   You are prevented from creating your own Private DNS Zones for supported PaaS services within your landing zone subscriptions. This is enforced by a "Deny" policy. This ensures consistency and prevents conflicts.
    *   Policies will automatically create the necessary DNS records in the centralized Private DNS Zones when you deploy a Private Endpoint. This is handled by "DeployIfNotExists" policies. You generally don't need to manually manage DNS for Private Endpoints.
*   **DNS Zone Groups:** When you create a private endpoint, the policy will automatically create a "private DNS zone group" and link it to the correct Private DNS Zone.

## Security requirements

*   **HTTPS Only:** Access to many services (like web apps, function apps, storage accounts, and APIs) will be enforced to use HTTPS only. HTTP (unencrypted) connections will be denied or automatically redirected to HTTPS.
*   **Minimum TLS Versions:**  For services that use TLS (Transport Layer Security) for encryption, a minimum TLS version (usually TLS 1.2) is enforced. Older, less secure versions of TLS are not allowed.
*   **Microsoft Defender for Cloud (MDFC)** is configured in the subscription.
*   **Security Contacts:** Security contact information (email addresses) must be configured at the subscription level so that Microsoft can notify you of security incidents. This will be automatically configured.
*   **No Weak or Outdated Protocols:** Services like storage accounts and Event Hubs are not allowed to use insecure protocols like old versions of SMB, Kerberos or insecure channel encryption methods.

## Data protection

### Customer-managed keys (CMK)

For many services, data *must* be encrypted at rest using Customer-Managed Keys (CMK). This means you manage the encryption keys in Azure Key Vault, giving you greater control and auditability.  Services that may require CMK include:

*   Azure Storage (blobs, files, queues, tables)
*   Azure Cosmos DB
*   Azure SQL Database (Transparent Data Encryption - TDE)
*   Azure Kubernetes Service (AKS) disks
*   Azure Machine Learning workspaces
*   Azure Cognitive Services
*   Azure Data Factory
*   Azure Event Hubs (Premium tier)
*   Azure Service Bus
*   Azure Key Vault
*   Azure Managed HSM
*   Azure Disk Encryption
*   Azure Data Box jobs
*   Azure Synapse Analytics workspaces
*   Azure API for FHIR

### Key vault requirements

*   **Deletion Protection:**
    * Soft delete is *enabled* on all Key Vaults, preventing accidental or malicious permanent deletion of secrets, keys, and certificates
    * Purge protection is *enabled* on all Key Vaults, adding an extra layer of protection against permanent deletion
*   **Access Control:**
    * Key Vault Firewall is enabled on all Key Vaults, and configured to *Deny All* traffic
    * Key Vault must use Azure RBAC - policy will deny creation using access policies
*   **Keys:**
    * Must have expiration dates set
    * Minimum RSA key sizes are enforced
    * Policies audit if keys are close to expiration
*   **Managed HSM Keys:**
    * Must have expiration date
    * Must use allowed cryptography curve names  
    * Cannot be active longer than specified days
    * Expired keys must be deleted
*   **Secrets:**
    * Must have expiration date
    * Cannot be active longer than specified days
    * Must have content type set
    * Policies audit if close to expiration
*   **Certificates:**
    * Must use integrated certificate authority
    * Cannot use non-integrated certificate authorities
    * Must have lifetime actions triggered at specific percentage

### Storage account security

*   **Data Loss Prevention:** Azure Storage accounts should restrict the allowed copy scope.
*   Storage Accounts with custom domains assigned are denied.
*   **No SFTP**: Storage Accounts are not allowed to have SFTP support enabled for Blob storage.
*   **No local users:** Storage accounts *cannot* use local users for features like SFTP. Authentication should be managed through Azure AD.

## Cost optimization

*   **Unused Resources:** Policies will *audit* for resources that are potentially unused and driving unnecessary costs, such as:
    *   Unattached disks.
    *   Unused public IP addresses.
    *   Empty App Service plans.

## Identity & access management

* Access is managed through EntraID security groups with standardized naming: `DO_PuC_Azure_Live_{LicensePlate}_{Role}`
* Three standard permission levels are enforced:
    * Reader: View-only access to resources
    * Contributor: Can create and manage resources but cannot modify access permissions 
    * Owner: Administrative access with full control (with security restrictions)
* Direct role assignments are not allowed - all access must be granted through security groups
* Security groups are assigned roles at the Management Group level which propagates to all subscriptions
* Service principals can be directly assigned roles on resources
* All actions are subject to Azure Policy restrictions regardless of role
* See [User management](../design-build-deploy/user-management.md) for complete details

## Specific service guardrails

### Azure Kubernetes Service (AKS)

*   No privileged containers.
*   Restrictions on capabilities that containers can request.
*   Enforcement of HTTPS ingress.
*   No use of the `default` namespace.
*   AKS clusters must use a minimum TLS version of 1.2.

### Azure SQL Database/Managed Instance

*   Auditing must be enabled.
*   Transparent Data Encryption (TDE) must be enabled.
*   Threat detection must be enabled.
*   Minimum TLS version enforced.
*   No public endpoint (Private Endpoints enforced).
*   AAD-only authentication enforced.

### Azure Storage

*   Secure transfer (HTTPS) required.
*   Minimum TLS version enforced.
*   No public blob access.
*   Container delete retention policies (for data loss protection).
*   Restrictions on Cross-Origin Resource Sharing (CORS) rules.
*   No local users.
*   Restrictions on storage account keys.

### Azure Automation

*   No child resources (runbooks, variables, modules, credentials) can be created directly within the Automation Account.  These should be managed through a proper deployment pipeline.

### Azure Machine Learning

*   Restrictions on public network access.
*   Enforcement of high business impact workspaces.
*   Limitations on VM sizes for compute clusters.
*   Mandatory subnet connectivity for compute clusters and instances.
*   Restrictions on remote login port public access for compute clusters.
*   Restrictions on node count for compute clusters.
*   Restrictions on the idle timeout before scaledown.

### Azure Databricks

*   Requires the use of VNet injection (no public workspaces).
*   Limits to Premium SKU.
*   Requires No Public IP be set to true on Databricks.

### Other services

*   **Azure Logic Apps, API Management, Cognitive Services, Event Hub, Data Factory, etc.:** Similar restrictions around public network access, minimum TLS versions, and use of customer-managed keys.
*   **Azure Monitor**: All monitor services will deploy diagnostic settings.
*   **Logic Apps**: Logic apps will only be accessible over HTTPS.

## Monitoring

*   **Diagnostic Settings:** Resources will be configured to send their diagnostic logs and metrics to a central Log Analytics workspace.
*   **Azure Monitor Agent (AMA):**  VMs and VM Scale Sets will be configured to use the Azure Monitor Agent for data collection.
*   **Update Management:** Automatic OS update assessment will be enabled.
*   **Defender for Cloud:** Settings enforced to provide security information and recommendations.
*   **No Deletion of Diagnostic Settings:** Deletion of the configured diagnostic settings is *prevented*.

## Tagging

*   **Tag Inheritance:**  Resources and resource groups will automatically inherit specific tags (e.g., "Account Code," "Billing Group," "Ministry Name") from the subscription. This ensures consistent tagging across the environment.

## Important considerations

*   **Exceptions:** In rare cases, exceptions to these policies *may* be granted, but this would require a formal review and justification process.
*   **"Audit" vs. "Deny":**  "Audit" policies will report on non-compliance, but will *not* block deployments.  "Deny" policies will actively *prevent* non-compliant deployments. "DeployIfNotExists" or "Modify" policies will attempt to automatically configure resources to be compliant.
*   **Order of policy enforcement:** If there are policies that are both "Audit" and "Deny" then, deny policies will always take precedence.
*   **Changes:** This document, and the policies themselves, are subject to change. We will communicate any significant updates.

!!! info "Technical details"
    This document provides a high-level overview. For specific technical details, please refer to the Azure Policy definitions themselves or [contact the platform team](https://citz-do.atlassian.net/servicedesk/customer/portal/3).