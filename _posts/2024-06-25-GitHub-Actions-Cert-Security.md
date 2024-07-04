---
layout: single
title:  "GitHub Actions Cert Prep: Security"
date:   2024-06-25 12:00:00 -0500
toc: true
toc_icon: "cloud"
categories:
  - GitHub
tags:
  - GitHub
---

Personal notes for preparing for the GitHub Actions Certification
{: .notice--info}

## Code Security

### Manage Dependabot PRs

Dependabot creates pull requests to upgrade dependencies. You can configure your repository to get pull requests for version updates and/or security updates from Dependabot. You handle these pull requests like any other pull request, but you can also use some additional commands.

### Automating Dependabot with GitHub Actions

GitHub Actions workflows can run on Dependabot's pull requests and comments, but some events have different behaviors.

When Dependabot (github.actor == 'dependabot[bot]') starts workflows using the pull_request, pull_request_review, pull_request_review_comment, push, create, deployment, and deployment_status events, these limitations apply:
- GITHUB_TOKEN has read-only permissions by default.
- Secrets are populated from Dependabot secrets. GitHub Actions secrets are not available.
- For workflows initiated by Dependabot (github.actor == 'dependabot[bot]') using the pull_request_target event, if the base ref of the pull request was created by Dependabot (github.actor == 'dependabot[bot]'), the GITHUB_TOKEN will be read-only and secrets are not available.