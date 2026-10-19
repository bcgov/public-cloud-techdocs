# Resolve Azure private endpoints from on-premises

Last updated: **{{ git_revision_date_localized }}**

Use this page when an on-premises system must reach a private Azure resource. It explains why the public name fails today. It shows how to send only the needed service namespaces to the Azure inbound resolver. It also describes the policy work now in progress.

---

## Why this problem exists

Some Azure services must stay on private networking. Databases and Key Vaults are common examples. Each of these services uses a [private endpoint](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview).

The Azure Landing Zone already hosts the private DNS zones for those services. Azure resources resolve the private IP without extra work.

On-premises DNS is different. On-premises servers forward only Private Link names to Azure. An example is `mydb.privatelink.database.windows.net`.

Most Azure clients do not use that Private Link name. They use the public name. An example is `mydb.database.windows.net`. The TLS certificate is also on that public name.

Public DNS then returns a public IP. The client tries the public endpoint. The connection fails when public access is off. Teams then add a hosts file entry as a workaround.

Microsoft's supported pattern is different. Forward the **public** service zone to Azure DNS. Azure then follows the CNAME to the Private Link zone. The client still uses the public name. The answer is the private IP.

See [Azure Private Endpoint DNS integration](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-dns-integration).

!!! info "Supported on-premises zones"
    Resolution from on-premises works in the `Internal` and `DMZ` zones only. If you are in another zone, [submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team.

You also need [ExpressRoute](networking-express-route.md) and the [MCCS firewall requests](../../shared-services/mccs/mccs.md). DNS setup does not replace those steps.

## Current state

Get the inbound resolver address from the [inbound DNS addresses](https://digital.gov.bc.ca/technology/cloud/public/internal-resources/internal-government-and-external-public-connections/#inbound-dns). Send selected Azure service namespaces to that address. Leave every other name on your normal DNS servers.

!!! note "Every environment"
    The same page lists the inbound resolver for each environment. Use the address for the environment you connect to.

### Prefer a namespace rule

You do not need one hosts file line for each resource. Use the full service namespace. Point that namespace at the inbound resolver.

On Windows, use a Name Resolution Policy Table (NRPT) rule.

```powershell
Add-DnsClientNrptRule `
  -Namespace ".database.windows.net" `
  -NameServers "<inbound-resolver>"
```

The leading dot covers every name in that zone. One rule can serve many Azure SQL databases. Replace `<inbound-resolver>` with the address from the [inbound DNS addresses](https://digital.gov.bc.ca/technology/cloud/public/internal-resources/internal-government-and-external-public-connections/#inbound-dns).

Confirm the rule:

```powershell
Get-DnsClientNrptRule
```

!!! tip "Use the public service zone"
    Forward `database.windows.net`. Do not forward only `privatelink.database.windows.net`. Microsoft requires the public zone so the CNAME chain can complete.

!!! warning "Keep each rule narrow"
    Add a rule only for a service you reach over a private endpoint. Read [Send only the namespaces you need](#send-only-the-namespaces-you-need) before you add a parent zone such as `microsoft.com`.

### Use a hosts file only for a short test

A hosts file maps one name to one IP. The IP can change when the private endpoint is recreated. Microsoft recommends a hosts file only for testing.

If you still need a hosts file, use the **public** name. TLS checks that name.

```text
10.x.x.x    mydb.database.windows.net
```

!!! tip "Hosts files do not scale"
    Add a namespace rule instead of a growing hosts file. The inbound resolver already has the current private IP. Keep that rule to one service zone.

### Other platforms

This is not a Windows-only need. Any on-premises system that starts a connection may need the same split DNS.

Use the OS tools that send one domain to a specific nameserver. Examples include `systemd-resolved` on Linux and resolver files on macOS. Use the address from the [inbound DNS addresses](https://digital.gov.bc.ca/technology/cloud/public/internal-resources/internal-government-and-external-public-connections/#inbound-dns).

Linux example with `systemd-resolved`. Use a dedicated link. Keep corporate DNS on the default route.

```ini
# /etc/systemd/network/azure-dns.netdev
[NetDev]
Name=azure-dns
Kind=dummy
```

```ini
# /etc/systemd/network/azure-dns.network
[Match]
Name=azure-dns

[Network]
DNS=<inbound-resolver>
Domains=~database.windows.net
DNSDefaultRoute=no
```

The `~` prefix sends that domain to the inbound resolver. `DNSDefaultRoute=no` keeps other names off that link. A global `DNS=` entry also receives names that match no routing domain. Leave the inbound resolver out of that list.

## Send only the namespaces you need

Keep your normal DNS servers for day-to-day name resolution. Send the inbound resolver only the public service zones for private endpoints you use.

The inbound resolver is the right target for those zones. It is the wrong target for all DNS, and for broad Microsoft zones such as `microsoft.com`, `windows.net`, `azure.com`, or `office.com`.

### The private path becomes required

Queries to the inbound resolver travel over ExpressRoute. Microsoft requires [ExpressRoute or a VPN](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview) for DNS between on-premises and Azure. A firewall on that path must also allow DNS.

If ExpressRoute, that firewall, or the inbound endpoint is unavailable, every name you send there fails. Names left on your normal DNS servers keep working.

Public Microsoft DNS does not depend on that private path. Sign-in, Microsoft 365, Windows Update, and the Azure portal are designed to resolve on the internet. A wide rule makes the inbound resolver a single point of failure for those services.

### A matching rule does not fall back

Windows checks the NRPT before it uses the DNS servers on the network adapter. When a name matches a rule, Windows asks only the servers in that rule.

Microsoft documents the result for NRPT namespace rules. If those servers are unreachable, the client does not ask the computer's normal DNS servers. See [Design your DNS infrastructure for DirectAccess](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee382323(v=ws.10)).

The same split applies on Linux when a routing domain lists only the inbound resolver. An outage of the private path is then an outage for every matching name.

### A private zone answers for the whole namespace

The landing zone links private DNS zones for Azure services. A linked private zone is authoritative for that namespace.

Microsoft warns against overriding a zone that still resolves public endpoints. Without fallback, a missing private record returns `NXDOMAIN`. The lookup then stops, even when public DNS has an address. See [Azure Private Endpoint private DNS zone values](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-dns).

The landing zone enables [fallback to the internet](https://learn.microsoft.com/en-us/azure/dns/private-dns-fallback) on these private zone links. Azure retries the query on public DNS when the private zone has no record. A public resource in that same service zone can still resolve.

Fallback runs only after the query reaches the inbound resolver address. It does not help when ExpressRoute or the resolver is unavailable. It is not a reason to forward `microsoft.com` or all DNS.

### The resolver is not the corporate DNS service

An inbound endpoint can still answer other names through Azure DNS when no private zone matches. That ability does not make it a replacement for corporate DNS.

Microsoft documents a limit of 10,000 queries per second for each endpoint. See [Azure DNS Private Resolver](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview). Private-endpoint lookups fit that role. DNS for every workstation and every Microsoft service does not.

## Common namespaces

Use a public service zone from this table. These are common examples:

| Service | Public namespace to forward |
| --- | --- |
| Azure SQL Database | `database.windows.net` |
| Azure Database for PostgreSQL | `postgres.database.azure.com` |
| Azure Storage (Blob) | `blob.core.windows.net` |
| Azure Storage (File) | `file.core.windows.net` |
| Azure Key Vault | `vault.azure.net` |
| Azure App Service | `azurewebsites.net` |
| Azure Container Registry | `azurecr.io` |
| Azure OpenAI | `openai.azure.com` |

See the full list in [Azure Private Endpoint private DNS zone values](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-dns).

Storage and some other services use more than one namespace. Add a rule for each namespace you use. Leave the rest of Microsoft DNS on your normal DNS servers.

## Test the lookup

Ask the inbound resolver for the public name. You should get a private IP.

```powershell
Resolve-DnsName mydb.database.windows.net -Server <inbound-resolver>
```

```bash
nslookup mydb.database.windows.net <inbound-resolver>
```

Then test from the client after you add the rule. Use `Resolve-DnsName` on Windows. `nslookup` does not use the NRPT. Flush the local DNS cache if the old public IP still appears.

Also look up a Microsoft name you did not forward, such as `login.microsoftonline.com`. That name should still use your normal DNS servers.

!!! tip "Name resolution is not enough"
    A private IP in DNS does not open the path by itself. Confirm ExpressRoute and both firewall requests are in place.

!!! tip "Allow DNS to the inbound resolver"
    Your on-premises firewall must allow DNS to the inbound resolver. Use UDP and TCP port 53. Allow that path only for the systems that use these rules.

## What we are working on

The Public Cloud team is working with Endpoint Engineering and Standards. The goal is a targeted policy for selected devices.

The first policy applies NRPT rules to selected Windows devices through Intune or Group Policy. Linux systems are in scope for a later policy. Until that policy exists, configure Linux locally, as shown above.

Those rules use the same narrow service namespaces. They send only those zones to the inbound resolver. The reasons in [Send only the namespaces you need](#send-only-the-namespaces-you-need) still apply.

This work is not ready to request yet. There is no intake form for the policy today. Workarounds on this page remain the current path.

The endpoint team currently covers Windows workstations. On-premises servers still need a local namespace rule.

!!! note "Want to be included later"
    [Submit a Service Request](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to the Public Cloud team. Include the Azure service namespaces and the systems that need them.

The team is also exploring a central source for the resolver address. That source makes a later change or failover easier. It does not replace a narrow rule.

---

## Related pages

- [On-premises connectivity with Azure ExpressRoute](networking-express-route.md)
- [Networking within the Azure Landing Zone](networking.md)
- [Private endpoints and DNS](../best-practices/be-mindful.md#private-endpoints-and-dns)
- [Multi-Cloud Connectivity Service (MCCS)](../../shared-services/mccs/mccs.md)
- [Public cloud support](../../welcome/support.md)
- [Inbound DNS addresses](https://digital.gov.bc.ca/technology/cloud/public/internal-resources/internal-government-and-external-public-connections/#inbound-dns)
- [Azure Private Endpoint DNS integration](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-dns-integration)
- [Azure Private Endpoint private DNS zone values](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-dns)
- [Azure DNS Private Resolver](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview)
