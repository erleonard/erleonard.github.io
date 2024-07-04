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

## Contexts
Contexts are objects that have properties, which can be strings or other objects. They let you access information about different aspects of workflow runs, such as variables, runner environments, jobs, and steps.

| Context name | Type    | Description                                                                                   |
|--------------|---------|-----------------------------------------------------------------------------------------------|
| github       | object  | Information about the workflow run. For more information, see github context.                 |
| env          | object  | Contains variables set in a workflow, job, or step. For more information, see env context.    |
| vars         | object  | Contains variables set at the repository, organization, or environment levels. For more information, see vars context. |
| job          | object  | Information about the currently running job. For more information, see job context.           |
| jobs         | object  | For reusable workflows only, contains outputs of jobs from the reusable workflow. For more information, see jobs context. |
| steps        | object  | Information about the steps that have been run in the current job. For more information, see steps context. |
| runner       | object  | Information about the runner that is running the current job. For more information, see runner context. |
| secrets      | object  | Contains the names and values of secrets that are available to a workflow run. For more information, see secrets context. |
| strategy     | object  | Information about the matrix execution strategy for the current job. For more information, see strategy context. |
| matrix       | object  | Contains the matrix properties defined in the workflow that apply to the current job. For more information, see matrix context. |
| needs        | object  | Contains the outputs of all jobs that are defined as a dependency of the current job. For more information, see needs context. |
| inputs       | object  | Contains the inputs of a reusable or manually triggered workflow. For more information, see inputs context. |

## Variables

### Configuration Variables
You can create configuration variables for use across multiple workflows, and can define them at either the **organization**, **repository**, or **environment** level.

question 22