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

### Configure DNS in an OpenShift cluster

An OpenShift workload can use one of these DNS options:

- Add a pod-level `hostAliases` entry for a single private endpoint.
- Forward one DNS zone to the Azure inbound resolver for all pods in the cluster.
- Run a namespace-local DNS service for selected workloads.

Use the public service name in each option. TLS uses that name to check the certificate.

#### Add a host alias to one pod

The `hostAliases` property adds an entry to the pod's `/etc/hosts` file. Use it for a short test. It also works when one pod needs one name.

Add `hostAliases` to the pod spec or pod template:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: key-vault-client
spec:
  selector:
    matchLabels:
      app: key-vault-client
  template:
    metadata:
      labels:
        app: key-vault-client
    spec:
      hostAliases:
        - ip: 10.x.x.x
          hostnames:
            - example.vault.azure.net
      containers:
        - name: key-vault-client
          image: <container-image>
```

Replace the IP, host name, and image with your values. A host alias becomes stale if the private endpoint's IP changes.

!!! warning "Use host aliases only for narrow cases"
    A host alias applies only to one pod spec. Use DNS zone forwarding when many workloads need the same service zone.

#### Forward one zone from the cluster

Configure the OpenShift DNS operator to forward a public service zone. Send that zone to the Azure inbound resolver. The rule applies to every pod that uses cluster DNS.

```yaml
apiVersion: operator.openshift.io/v1
kind: DNS
metadata:
  name: default
spec:
  servers:
    - name: azure-private-link
      zones:
        - vault.azure.net
      forwardPlugin:
        policy: Sequential
        upstreams:
          - <inbound-resolver>
```

Apply this configuration to the cluster:

```bash
oc apply -f azure-private-link-dns.yaml
```

Replace `<inbound-resolver>` with an address from the [inbound DNS addresses](https://digital.gov.bc.ca/technology/cloud/public/internal-resources/internal-government-and-external-public-connections/#inbound-dns). Add only the service zones your workloads use.

!!! note "Zone forwarding is cluster-wide"
    The OpenShift DNS operator does not support zone forwarding for one namespace. This rule applies to every pod that uses cluster DNS.

OpenShift uses the cluster resolver for this rule. A pod's `dnsConfig.nameservers` setting applies to all queries. Use the DNS operator configuration to forward one zone.

The OpenShift cluster must reach the inbound resolver over the private network. Allow UDP and TCP port 53. Confirm [ExpressRoute](networking-express-route.md) and the [MCCS firewall requests](../../shared-services/mccs/mccs.md).

#### Forward one zone from a namespace

Run a namespace-local CoreDNS service when only some workloads need a zone override. This example sends `vault.azure.net` to the Azure inbound resolver. It sends every other query to the OpenShift cluster DNS service.

Get the cluster DNS service IP:

```bash
oc -n openshift-dns get service dns-default \
  -o jsonpath='{.spec.clusterIP}{"\n"}'
```

Use this CoreDNS configuration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: namespace-dns
  namespace: <namespace>
data:
  Corefile: |
    vault.azure.net:53 {
        errors
        cache 30
        forward . <inbound-resolver>
    }

    .:53 {
        errors
        cache 30
        forward . <openshift-cluster-dns-ip>
    }
```

Replace `<inbound-resolver>` and `<openshift-cluster-dns-ip>` with your values. Run CoreDNS with this configuration in the namespace. Expose it through a ClusterIP service on UDP and TCP port 53.

Deploy CoreDNS and expose it inside the namespace:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: namespace-dns
  namespace: <namespace>
spec:
  replicas: 1
  selector:
    matchLabels:
      app: namespace-dns
  template:
    metadata:
      labels:
        app: namespace-dns
    spec:
      containers:
        - name: coredns
          image: registry.k8s.io/coredns/coredns:v1.11.1
          args:
            - -conf
            - /etc/coredns/Corefile
          ports:
            - containerPort: 53
              protocol: UDP
            - containerPort: 53
              protocol: TCP
          volumeMounts:
            - name: config
              mountPath: /etc/coredns
              readOnly: true
      volumes:
        - name: config
          configMap:
            name: namespace-dns
---
apiVersion: v1
kind: Service
metadata:
  name: namespace-dns
  namespace: <namespace>
spec:
  selector:
    app: namespace-dns
  ports:
    - name: dns-udp
      port: 53
      protocol: UDP
      targetPort: 53
    - name: dns-tcp
      port: 53
      protocol: TCP
      targetPort: 53
```

Replace `<namespace>` and the CoreDNS image with approved values. Apply the ConfigMap, deployment, and service in the same namespace.

Configure the selected workload to use that service IP:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: key-vault-client
  namespace: <namespace>
spec:
  selector:
    matchLabels:
      app: key-vault-client
  template:
    metadata:
      labels:
        app: key-vault-client
    spec:
      dnsPolicy: None
      dnsConfig:
        nameservers:
          - <namespace-core-dns-service-ip>
        searches:
          - <namespace>.svc.cluster.local
          - svc.cluster.local
          - cluster.local
        options:
          - name: ndots
            value: "5"
      containers:
        - name: key-vault-client
          image: <container-image>
```

Replace the namespace, CoreDNS service IP, and image with your values. The `nameservers` field requires an IP address. It does not accept a service name.

Update each affected pod template. OpenShift does not apply `dnsPolicy: None` to every pod in a namespace.

This approach gives you namespace-level control. It also adds a DNS service to operate. Use cluster-wide forwarding when all namespaces can share the zone rule.

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
