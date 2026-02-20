# Public Cloud technical documentation

![Lifecycle: Active](https://img.shields.io/badge/Lifecycle-Active-97ca00)

This is a single location for all technical/developer documentation for product teams working on the Public Cloud Platform.

Visit the deployed Gatsby site at [docs.developer.gov.bc.ca](https://developer.gov.bc.ca/). See how that's deployed at the [private-cloud-techdocs](https://github.com/bcgov/platform-developer-docs/tree/07904f54dc36c33db156145945be6c00b62483d2) repo.

# Documentation

Markdown documents are located in `./docs` with related images in `./images`.

In progress (not yet published) documents are located in `./drafts`.

Please see [Writing guide for Platform Services technical documentation](https://github.com/bcgov/platform-developer-docs/blob/07904f54dc36c33db156145945be6c00b62483d2/tech-docs-writing-guide.md) for documentation formatting and contribution information.

Start your new document from the new [Markdown document template](/new-markdown-document-template.md).

## Call-Outs

To add call-outs to your markdown content, please refer to the following links for supported types and syntax:
<https://developer.gov.bc.ca/docs/default/component/bc-developer-guide/content-syntax-guide/#admonitions>
<https://squidfunk.github.io/mkdocs-material/reference/admonitions/#supported-types>

## HTML Link Validation

The build process will validate all links in the markdown files. If a link is broken or unreachable, the build will fail. For more information on how the validation works, along with how to add exceptions, please refer to the following link:

- [https://github.com/bcgov/devhub-techdocs-publish/blob/stable/docs/index.md#link-validation-using-htmltest](https://github.com/bcgov/devhub-techdocs-publish/blob/stable/docs/index.md#link-validation-using-htmltest)
- [https://github.com/wjdp/htmltest](https://github.com/wjdp/htmltest)

# Deploy locally

To deploy locally you need to have Node.js installed. If you don't have it installed, you can download it from [here](https://nodejs.org/en/download/). You will also need to have Docker installed. If you don't have it installed, you can download it from [here](https://www.docker.com/products/docker-desktop).

After you have Node.js and Docker installed, you can follow these instructions to [preview content locally](https://github.com/bcgov/devhub-techdocs-publish/blob/main/docs/index.md#how-to-use-the-docker-image-to-preview-content-locally). A browser should open with an un-themed, but formatted version of the documentation. This closely resembles what will be visible on the end product.

## Windows Users

If you are using Windows, the command provided in the instructions will not work. You have 2 options, either execute a modified command (listed below) from a PowerShell terminal, or execute the command from a Windows Subsystem for Linux (WSL) terminal.

> NOTE: The site will not auto-launch in your browser. You will need to click on the link provided in the terminal output (ie. <http://localhost:3000>).

**PowerShell Terminal Command:**

```powershell
docker run -it -p 3000:3000 -v ${PWD}:/github/workspace ghcr.io/bcgov/devhub-techdocs-publish preview
```

**WSL Terminal Command:**

```wls
docker run -it -p 3000:3000 $(pwd):/github/workspace ghcr.io/bcgov/devhub-techdocs-publish preview
```

> NOTE: If you are executing the command from a WSL terminal, you will need to have the repository cloned into your WSL environment.
