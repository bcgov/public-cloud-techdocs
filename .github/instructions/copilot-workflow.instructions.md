# GitHub Copilot workflow instructions - public-cloud-techdocs

These instructions define repository workflow defaults for Copilot-assisted execution.

## Pull request target branch

Use this pull request flow by default:

1. Create feature branch PRs into `dev`.
2. Promote `dev` to `main` in a separate PR.

Do not open feature branch PRs directly to `main` unless a maintainer explicitly asks for an exception.

## Pull request summary standard

When creating a PR with Copilot tooling:

- Use a Copilot-generated PR title and summary/body when supported.
- Keep the summary concise and accurate.
- Include the scope of changes and key outcomes.

If Copilot-generated summary is not available in the current toolchain, use a manual summary with:

- Summary
- Notes

## Documentation naming convention

In this repository, treat "Public Cloud team" as a formal name and keep this capitalization in documentation updates.

When updating or reviewing documentation with MkDocs admonitions, validate that `!!!` blocks use aligned 4-space indentation and supported admonition types.
