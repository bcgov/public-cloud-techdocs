# App Service

Last updated: **{{ git_revision_date_localized }}**

Use this page when you delete an App Service or need to restore one.

---

## Remove virtual network integration before you delete

If you use an [App Service](https://learn.microsoft.com/en-us/azure/app-service/overview), and you plan to [integrate it with an Azure Virtual Network](https://learn.microsoft.com/en-us/azure/app-service/overview-vnet-integration), keep in mind this limitation: **You can't delete a subnet that has previously had an integrated App Service, if the integration has not been removed**.

To follow best practices when using Azure App Services with VNet integration, if you plan to delete the App Service, ensure that you **remove the integration** with the Virtual Network **before** deleting the App Service. This will allow you to delete the associated Subnet without any issues.

!!! failure "Subnet deletion failure"
    If you forget to remove the integration, and have deleted the App Service, you will need to [open a support case](../support/enterprise-support.md#how-to-receive-support) with Microsoft Support to have the integration removed before you can delete the Subnet.

## Restore a deleted App Service

Azure keeps a deleted App Service for 30 days. You can restore it during that time. The 30-day period starts when you delete the app.

Open **Manage Deleted Apps** in the subscription that owned the app. The list includes only apps deleted in the last 30 days. An app on that list is still available to restore. Restore it before the 30 days end.

!!! bug "Portal restore error"
    The portal can show this error after you select a deleted app:

    `The provided subscription identifier 'resourceGroups' is malformed or invalid`

    This error is a known Azure portal bug. Your subscription is valid. Microsoft is investigating the same error. Read [I cannot restore my deleted App Service](https://learn.microsoft.com/en-us/answers/questions/5857538/i-cannot-restore-my-deleted-app-service) for the April 2026 report.

After you select the deleted app, the portal calls Azure Resource Manager with an empty subscription ID. Azure Resource Manager reads the next path segment, `resourceGroups`, as the subscription ID. It returns `InvalidSubscriptionId`.

The portal can only replace an existing app of the same kind in the same region. The page fails before you can choose a replacement app. Restore with Azure CLI or Azure PowerShell. Both tools can create the app again when the App Service plan still exists.

Follow the [steps to restore a deleted App Service](https://learn.microsoft.com/en-us/azure/app-service/app-service-undelete).

### Restore with Azure CLI

1. Confirm the deleted app. Run this command in the subscription that owned the app. Record the deletion time from the result.

    ```bash
    az webapp deleted list --query "[?name=='<app-name>']" -o json
    ```

    See the [az webapp deleted command reference](https://learn.microsoft.com/en-us/cli/azure/webapp/deleted).

2. Restore the app. The resource group must already exist. If you deleted the App Service plan as well, create a new plan in the same region first. Use a Linux plan for a Linux container app. Then run:

    ```bash
    az webapp deleted restore \
      --deleted-id "<deleted-id>" \
      --resource-group "<resource-group>" \
      --name "<app-name>" \
      --target-app-svc-plan "<app-service-plan>"
    ```

If several deleted sites share that name, use the deleted-site ID from the list. Pass it with `--deleted-id`.

### Restore with Azure PowerShell

List the deleted app in the region where it ran. Use `canadacentral` or `canadaeast`.

```powershell
Get-AzDeletedWebApp -Name "<app-name>" -Location "<region>"
```

Restore the app into the original resource group:

```powershell
Restore-AzDeletedWebApp `
  -ResourceGroupName "<resource-group>" `
  -Name "<app-name>" `
  -TargetResourceGroupName "<resource-group>" `
  -TargetName "<app-name>" `
  -TargetAppServicePlanName "<app-service-plan>"
```

See [Restore-AzDeletedWebApp](https://learn.microsoft.com/en-us/powershell/module/az.websites/restore-azdeletedwebapp). If several deleted sites share that name, pass `-DeletedId` with the ID from the list.

### Limits on restore

These limits still apply after a successful restore:

- Only a paid plan supports this restore. A Linux container app already meets this rule.
- Add custom domains, certificates, and bindings again after the restore.
- Recreate deployment slots after the restore.
- For kind `app,linux,container`, the restore returns the site configuration. That includes the container image and app settings.

### Update Terraform before the next apply

Update your Terraform configuration before the next apply. If you skip this step, the same run deletes the restored app. Import the restored app into state when Terraform should own it.

See [Infrastructure-as-Code (IaC) best practices](../best-practices/iac.md).

### When the command-line restore fails

Open an [Azure support request](../support/enterprise-support.md#how-to-receive-support) when Azure CLI and PowerShell both fail. Open it on the subscription that owned the app. Include the app name, region, deletion time, and the error. Copy the deletion time from `az webapp deleted list`.

Ask Microsoft to restore the site on the server. Make the request while the app is still inside the 30-day window.
