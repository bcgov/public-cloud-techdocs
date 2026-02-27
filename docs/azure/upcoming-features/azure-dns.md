# Azure DNS resolution for on-premises connectivity

Last updated: **{{ git_revision_date_localized }}**

This page explains the current DNS gap between on-premises networks and Azure.
It also shows the temporary workaround that teams use today.

---

## Overview

DNS resolves host names to IP addresses.
This lets resources in Azure and on-premises networks communicate.

## Current status

Azure currently resolves on-premises names from Azure workloads.
On-premises networks do not yet resolve private Azure names through shared DNS.

## Current workaround

If you connect from on-premises to Azure, you can use a local `hosts` file entry.
Map the Azure resource FQDN to its private IP address.

The team is in the planning and design phase for full Azure DNS resolution.

## Related pages

- [On-premises connectivity with Azure ExpressRoute](../design-build-deploy/networking-express-route.md)
- [Domain join](domain-join.md)
