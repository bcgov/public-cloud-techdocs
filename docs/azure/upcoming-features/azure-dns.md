# Azure DNS resolution from on-premises

Last updated: **{{ git_revision_date_localized }}**

This page explains the current DNS gap between on-premises networks and Azure.
It also shows the temporary workaround that teams use today.

## Overview

DNS resolves host names to IP addresses.
This lets resources in Azure and on-premises networks communicate.

## Current status

Azure workloads can currently resolve on-premises DNS names.
On-premises networks cannot yet resolve private Azure DNS names through shared DNS.

## Current workaround

If you connect from on-premises to Azure, you can use a local `hosts` file entry.
Map the Azure resource FQDN to its private IP address.

### Important caveats

This workaround has significant operational limits:

- **Per-machine scope:** Each user must add the entry on their own machine; it does not scale across your organization.
- **Manual maintenance:** You must update or remove entries when Azure resources are recreated or their private IP addresses change.
- **No automatic refresh:** Changes to Azure resources are not reflected in your local `hosts` file.

Do not use this for production systems that require frequent IP address changes or many users.

### How to add an entry

The `hosts` file location depends on your operating system:

- **Windows:** `C:\Windows\System32\drivers\etc\hosts`
- **Linux/macOS:** `/etc/hosts`

Add a new line to the file with the format: `<private-ip>  <azure-fqdn>`

You need administrator or root access to edit this file.

### Example entry

```
192.168.1.50  myazuresql.database.windows.net
```

Replace `192.168.1.50` with the Azure resource's private IP address.
Replace `myazuresql.database.windows.net` with the Azure resource's fully qualified domain name.

### Refresh your DNS cache

After updating your `hosts` file, refresh your DNS cache:

- **Windows (Command Prompt or PowerShell):**
  ```
  ipconfig /flushdns
  ```

- **Linux:**
  ```
  sudo systemctl restart systemd-resolved
  ```

- **macOS:**
  ```
  sudo dscacheutil -flushcache
  ```

The team is in the planning and design phase for full Azure DNS resolution.

## Related pages

- [On-premises connectivity with Azure ExpressRoute](../design-build-deploy/networking-express-route.md)
- [Domain join](domain-join.md)
