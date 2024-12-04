# Public Cloud technical documentation
This is a single location for all technical/developer documentation for product teams working on the Public Cloud Platform.

Visit the deployed Gatsby site at [docs.developer.gov.bc.ca](https://developer.gov.bc.ca/). See how that's deployed at the [private-cloud-techdocs](https://github.com/bcgov/platform-developer-docs/tree/07904f54dc36c33db156145945be6c00b62483d2) repo.

# Documentation
Markdown documents are located in `./docs` with related images in `./images`.

In progress (not yet published) documents are located in `./drafts`.

Please see [Writing guide for Platform Services technical documentation](https://github.com/bcgov/platform-developer-docs/blob/07904f54dc36c33db156145945be6c00b62483d2/tech-docs-writing-guide.md) for documentation formatting and contribution information.

Start your new document from the new [Markdown document template](/new-markdown-document-template.md).

## Process to edit or create new content

As team members of the Public cloud, you can create Pull Requests (PR) and have two members of the team review it. 

Always consider these questions before editing to ensure it’s the best approach:

**1. Is it urgent?**
- **Yes**: Update the content immediately and get two team members to approve the PR. Let the content designer/strategist know afterward to keep them updated. Avoid large, urgent changes whenever possible; reserve this for emergencies
- **No**:  Please continue with the next question

**2. Is the information incorrect or outdated?**

- **Yes:** Make the needed updates right away. Notify the content designer/strategist afterward as the same information might appear on other pages
- **No:** Continue to the next question

**3. Are you updating a code sample?**

- **Yes:** You can make this change without involving the content designer/strategist, but do confirm any code changes with colleagues who can corroborate the changes
- **No:** Continue to the next question

**4. Is it a large change (more than two sentences)?**

- **Yes:** Contact the content designer/strategist with the details, including the deadline, so they can prioritize it
- **No:** Make the edit yourself, have two team members review it, and notify the content designer/strategist when done. For non-urgent changes, you can also add the content designer/strategist as a PR reviewer.

## For non-technical documentation on Digital Government – Province of British Columbia 

You can’t edit the [Public cloud website](https://digital.gov.bc.ca/cloud/services/public/) direcly as it requires WordPress editing access that the content designer/strategist has and/or some team members of the Public cloud team. For any updates, large or small, contact the content designer/strategist first.

 If the content designer/strategist is unavailable (due to vacation, illness, etc.), escalate the issue to Kaitlyn Rosenburg’s team at DO.contentdesign@gov.bc.ca and CC her. The Content DO team manages the entire [digital.gov.bc.ca](https://digital.gov.bc.ca)  site and can handle edits and special permissions in the content designer/strategist’s absence.

# Deploy locally - technical docs
To deploy locally you need to have Node.js installed. If you don't have it installed, you can download it from [here](https://nodejs.org/en/download/). You will also need to have Docker installed. If you don't have it installed, you can download it from [here](https://www.docker.com/products/docker-desktop).

After you have Node.js and Docker installed, you can run the following commands to deploy the documentation locally: `npx @techdocs/cli serve`. A browser should open with an un-themed, but formatted version of the documentation. This closely resembles what will be visible on the end product.

