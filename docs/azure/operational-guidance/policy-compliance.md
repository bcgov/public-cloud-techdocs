# Policy compliance

Last updated: **{{ git_revision_date_localized }}**

While the Public cloud team and the Security Operations team provide the initial guardrails and standards for the environment, **each ministry team is responsible** for managing their own resources and ensuring compliance with the standards.

---

## Azure policy

Azure policies have been applied within the Azure Landing Zones to help ensure that your resources stay compliant with  corporate standards. Some of these policies are assigned in "audit" mode, which means that they will not block the creation of non-compliant resources, but will log them for review.

Within the Azure portal, navigate to [Azure Policy](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyMenuBlade/~/Compliance). This service provides a centralized view of the compliance of your Azure environment. It also provides recommendations on how to improve your compliance.

![Azure policy overview](../images/azure-policy-overview.png "Azure policy overview")

For further guidance, please refer to the Microsoft documentation on [Determine causes of non-compliance](https://learn.microsoft.com/en-us/azure/governance/policy/how-to/determine-non-compliance).
