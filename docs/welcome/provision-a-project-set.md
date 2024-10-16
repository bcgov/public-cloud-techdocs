# Provision a new Project Set

Last updated: **October 3, 2024**

On the Public Cloud platform, different teams organize their work in isolated **Project Sets**. Before working in the cloud platform, the **Product Owner** of the respective team must submit a Project Set provisioning request for your team, through the [BC Platform Services Product Registry](https://registry.developer.gov.bc.ca/login).

---

## What is a Project Set?

A project set consists of four distinct environments for development (`dev`), testing (`test`), production (`prod`), and tooling (`tools`). This isolates and protects each stage of the deployment lifecycle.

- The **`dev`** account is for developers to experiment and test features
- The **`test`** account mirrors production and is used for quality assurance testing
- The **`prod`** account is the live environment accessed by end users
- The **`tools`** account contains shared resources like CI/CD pipelines, container registries, and automation tools

## Prerequisites

New requests must be reviewed and approved by the Public Cloud platform team. The same rule applies for teams that already have projects on the platform and require additional Project Sets.

To do that you must meet the prerequisites for provisioning a Project Set outlined in our [onboarding documentation](https://digital.gov.bc.ca/cloud/services/public/onboard/).

## Process to request a Project Set

1. Login to the [BC Platform Services Product Registry](https://registry.developer.gov.bc.ca/login)

  You will need to provide the following information:

  - A descriptive product name (no acronyms)
  - Contact details and IDIR accounts for the Product Owner and up to 2 Technical Leads
  - An estimate for the product's projected monthly spend on cloud services
    - For estimating AWS monthly costs, please use the [AWS Cost Calculator](https://calculator.aws/#/)
    - For estimating Azure monthly costs, please use the [Azure Pricing Calculator](https://azure.microsoft.com/en-ca/pricing/calculator/)
  - An AWS Account code, or Azure Subscription ID, for billing purposes. Refer to the Memorandum of Understanding (MoU) for more information.
    
  For **new product teams** requesting a Project Set in a Public Cloud Landing Zone, complete the following 2 steps before submitting the provisioning request:

  - Sign a Memorandum of Understanding (MoU) with the OCIO. Request an MoU template from <cloud.pathfinder@gov.bc.ca>.
  - Book an onboarding session with the Cloud Pathfinder team at <cloud.pathfinder@gov.bc.ca>.

2. Navigate to the top menu called **Public Cloud Products**.

  ![public-cloud](../images/provision-a-project-set/public-cloud.png)

3. On the top right side of the screen click on the button **Create +** to create a Project Set.

  ![create](../images/provision-a-project-set/create.png)

4. Enter the **name** and **description** of your project.

  - Project names should be descriptive. Avoid the use acronyms.

  ![description](../images/provision-a-project-set/description.png)

5. Enter your **ministry** and desired **service provider**.

  ![ministry-provider](../images/provision-a-project-set/ministry-provider.png)

6. Enter the **Product Owner** and **Technical Lead(s)** details per each required field.

  - The Product Owner and Technical Lead(s) will be granted access to the accounts/subscriptions in the Project Set via the Admin/Owner role.

  ![po-tech-lead](../images/provision-a-project-set/po-tech-leads.png)

7. Enter your **billing number**.

  - This number should be reflected on the team's signed MoU, and is related to the Expense Authority funding the project.

  ![billing](../images/provision-a-project-set/billing.png)

8. Enter your **estimated budgets**.

  - Budgets are a tool for the team to receive email billing alerts, so it's important that they are accurate. However they can be updated later.
  - You will receive a budget alert when your monthly spend has reached 50%, 80%, and 100% of your estimated monthly budget. This tool is intended to allow ministry teams to quickly react and control cost surges within the accounts.
  - For help estimating your budget please see the [costs and billing](https://digital.gov.bc.ca/cloud/services/public/intro/#costs) section of our introductory documentation.

  ![budget](../images/provision-a-project-set/budget.png)

## Accessing your Project Set

### AWS

Once the AWS accounts have been provisioned, the Product Owner and Technical Lead(s) will be able to see them all in the [Login Application](https://login.nimbus.cloud.gov.bc.ca/) and they will have Admin access into the accounts.

Other team members can be added to the Project Set by the Product Owner or Technical Lead(s) via the [AWS User Management](../aws/design-build-and-deploy-an-application/user-management.md) feature in the Product Registry.

### Azure

Once the Azure subscriptions have been provisioned, the Product Owner and Technical Lead(s) will be able to see them all in the [Azure Portal](https://portal.azure.com/) and they will have a restricted Owner role on the subscriptions.

Other team members can be added to the Project Set by the Product Owner or Technical Lead(s) directly in the [Azure Portal](https://portal.azure.com/). For more information on how to do this, see [Azure User Management](../azure/design-build-deploy/user-management.md).

---

## Related pages

- [Platform Project Registry](https://registry.developer.gov.bc.ca/login)
- [Onboard your team to public cloud hosting](https://digital.gov.bc.ca/cloud/services/public/onboard/)
- [AWS Landing Zone Overview](../aws/get-started/bc-govs-aws-landing-zone-overview.md)
- [Azure Landing Zone Overview](../azure/get-started/bc-govs-azure-landing-zone-overview.md)
