---
layout: single
title:  "GitHub Actions Cert Prep"
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


## Using Workflows: Workflow Commands

### Configuration Variables
You can create configuration variables for use across multiple workflows, and can define them at either the **organization**, **repository**, or **environment** level.

### Grouping log lines
Makes a collapsible section in the log. Use the **::group::** command with a title to make a group. Anything you print to the log from the group to the **::endgroup::** commands is inside a collapsible entry in the log.

```yaml
jobs:
  bash-example:
    runs-on: ubuntu-latest
    steps:
      - name: Group of log lines
        run: |
            echo "::group::My title"
            echo "Inside group"
            echo "::endgroup::"
```
### Creating starter workflows for your organization
Starter workflows help anyone in your organization who can create workflows to do it faster and simpler. When you make a new workflow, you can pick a starter workflow and some or all of the workflow will be written for you. You can use starter workflows as a base to create your own workflow or use them without changes. This saves time, and also encourages consistency and best practice across your organization.

Create a metadata file inside the `workflow-templates` directory. The metadata file must have the same name as the workflow file, but instead of the `.yml` extension, it must be appended with `.properties.json`. For example, this file named `octo-organization-ci.properties.json` contains the metadata for a workflow file named `octo-organization-ci.yml`.

```json
{
    "name": "Octo Organization Workflow",
    "description": "Octo Organization CI starter workflow.",
    "iconName": "example-icon",
    "categories": [
        "Go"
    ],
    "filePatterns": [
        "package.json$",
        "^Dockerfile",
        ".*\\.md$"
    ]
}
```

| Context name | Type    | Description                                                                                   |
|--------------|---------|-----------------------------------------------------------------------------------------------|
| name       | object  | Information about the workflow run. For more information, see github context.                 |
| description         | object  | Contains variables set in a workflow, job, or step. For more information, see env context.    |
| iconname         | object  | Contains variables set at the repository, organization, or environment levels. For more information, see vars context. |
| categories          | object  | Information about the currently running job. For more information, see job context.           |
| filePatterns         | object  | For reusable workflows only, contains outputs of jobs from the reusable workflow. For more information, see jobs context. |

### Adding a job summary

```bash
echo "{markdown content}" >> $GITHUB_STEP_SUMMARY
```
You can customize the Markdown for each job so that it appears on the summary page of a workflow run. Job summaries let you display and organize different content, such as test result summaries, so that someone who is looking at the result of a workflow run can see important information related to the run, such as failures, without having to look at the logs.

Job summaries support GitHub flavored Markdown, and you can add your Markdown content for a step to the `GITHUB_STEP_SUMMARY` environment file. `GITHUB_STEP_SUMMARY` is specific to each step in a job. For more information about the per-step file that `GITHUB_STEP_SUMMARY` refers to, see "Environment files."

When a job is done, the summaries for all steps in a job are combined into a single job summary and are shown on the workflow run summary page. If more than one job generates summaries, the job summaries are sorted by job completion time.


### Trigger workflow that requires manual approval by a user
To run a particular job in a workflow on demand, you can set up an environment that needs approval from a specific team or user. First, create an environment with required reviewers. Then, use the "environment:" key to mention the environment name in a job in your workflow. Any job that refers to the environment will wait until at least one reviewer approves the job.

To specify a certain person or team that must approve workflow jobs that use the `environment`, use required reviewers. You can name up to six users or teams as reviewers. The reviewers need to have read access to the repository at least. The job can continue if one of the required reviewers approves it.


## Manage workflow runs 

### Re-run workflows and jobs
Up to 30 days after a workflow run first runs, you can run it again, only run the jobs that failed in it, or run certain jobs in it. Used needs **write permissions** to re-run workflows in the repository.

Re-running a workflow or jobs in a workflow uses the same `GITHUB_SHA` (commit SHA) and `GITHUB_REF` (Git ref) of the original event that triggered the workflow run. The workflow will use the privileges of the actor who initially triggered the workflow, not the privileges of the actor who initiated the re-run. You can re-run a workflow or jobs in a workflow for up to 30 days after the initial run. You cannot re-run jobs in a workflow once its logs have passed their retention limits.

### Reuse workflows

**Limitations**
- You can connect up to four levels of workflows.
- You can call a maximum of 20 unique reusable workflows from a single workflow file. This limit includes any trees of nested reusable workflows that may be called starting from your top-level caller workflow file.
  - For example, top-level-caller-workflow.yml → called-workflow-1.yml → called-workflow-2.yml counts as 2 reusable workflows.
- Any environment variables set in an `env` context defined at the workflow level in the caller workflow are not propagated to the called workflow.
- Similarly, environment variables set in the `env` context, defined in the called workflow, are not accessible in the `env` context of the caller workflow. Instead, you must use outputs of the reusable workflow.
- To reuse variables in multiple workflows, set them at the organization, repository, or environment levels and reference them using the `vars` context.
- Reusable workflows are called directly within a job, and not from within a job step. You cannot, therefore, use `GITHUB_ENV` to pass values to job steps in the caller workflow.