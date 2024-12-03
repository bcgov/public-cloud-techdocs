# Public Cloud technical documentation
This is a single location for all technical/developer documentation for product teams working on the Public Cloud Platform.

Visit the deployed Gatsby site at [docs.developer.gov.bc.ca](https://docs.developer.gov.bc.ca/). See how that's deployed at the [private-cloud-techdocs](https://github.com/bcgov/private-cloud-techdocs) repo.

# Documentation
Markdown documents are located in `./docs` with related images in `./images`.

In progress (not yet published) documents are located in `./drafts`.

Please see [Writing guide for Platform Services technical documentation](https://github.com/bcgov/private-cloud-techdocs/blob/main/tech-docs-writing-guide.md) for documentation formatting and contribution information.

Start your new document from the new [Markdown document template](/new-markdown-document-template.md).

# Deploy locally
To deploy locally you need to have Node.js installed. If you don't have it installed, you can download it from [here](https://nodejs.org/en/download/). You will also need to have Docker installed. If you don't have it installed, you can download it from [here](https://www.docker.com/products/docker-desktop).

After you have Node.js and Docker installed, you can run the following commands to deploy the documentation locally: `npx @techdocs/cli serve`. A browser should open with an un-themed, but formatted version of the documentation. This closely resembles what will be visible on the end product.
