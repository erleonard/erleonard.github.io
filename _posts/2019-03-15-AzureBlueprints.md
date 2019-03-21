---
layout: single
title:  "Azure Blueprints: Intro"
date:   2019-03-15 14:17:12 -0500
toc: true
toc_icon: "cloud"
categories:
  - Azure
tags:
  - Azure
---

Governance for any cloud is very important and it’s imperative to take the time to put in place the proper guardrails to secure, control cost and monitor. Guardrails are put in place to set the boundary but not to stop progress.

Azure Blueprints allow you to create those guardrails on subscriptions. Blueprints allow you to orchestrate new environments quickly that meet policy and compliance needs of the customer.

Blueprints are composed of multiple artifacts into a single blueprint.  Artifacts can be policy assignments, role assignments, ARM templates and resource groups.

- Policy assignments are Azure policies that you can assignment a policy or an initiative. # Azure policies can be set to allow only certain resources to be deployed.  This can be even more refined with virtual machines by only selecting the approved VM size that you want to present.

- Role assignments are role-based access control that you can add an existing user or group to a role.

- ARM templates are where you have the most flexibility because you can create resources. An example of using arm templates can be to create a log analytics workspace or networking resources that will be shared across workloads.

- Resource groups are resource groups, this allows you to create the structure you want for your environment.

Let's have a look at how we can create, edit, assigned and delete a blueprint.

## Create and Edit blueprint
1. Go to the [Azure Portal](https://portal.azure.com)
2. Select **All services** and search for blueprints.
3. Under **blueprint definitions**, click **+ create blueprint**.
4. On the choose a blueprint page, select **start with blank blueprint**.
5. On the create blueprint page, enter the following information:
  - Enter a **blueprint name** and make to only include letters, numbers or dashes.
  - Enter a description for the blueprint.
  - In **definition location**, select a management group or subscription.
6. Select **Next: Artifacts**.
7. Artifacts page, this is where you can add policy assignments, role assignments, ARM templates and resource groups.
8. Once you are satisfied with your blueprint, select **Save draft**.

## Publish blueprint
Once you have finished making modifications to your blueprint, under **blueprint definitions**, right-click on your blueprint and select **Publish blueprint**.

The **Publish blueprint** blade will appear and provide a version number, change notes and click **Publish**. 

You are now ready to assign your blueprint to a management group or subscription.

## Assigned blueprint
To assign a blueprint follows almost the same steps a publishing blueprints.

1. Under **blueprint definitions**, right-click on the blueprint you have just created in the previous section, and select **assign blueprint**.
2. The **Assign blueprint** page will appear and enter the following information:
  - Subscription: Select a subscription
  - Assignment name: Enter a name
  - Location: select which Azure region you want to deploy to.
  - Blueprint definition version: select a version
  - Lock Assignment: select your lock preference.  You can choose to not lock, create a read-only lock or a do not delete lock.
  - Enter the required parameters for the resources.
  - Select **Assign** and the blueprint will not apply to the subscription you picked.

That was a quick overview of how you can create, edit and assign blueprints to management groups or subscriptions. Keep in mind that Azure blueprints are still in preview.

In conclusion, Azure blueprints will make your governance journey much easier by orchestrating the deployments of new environments.  But keep in mind that with any Governance projects the technical piece is always the smallest and you will spend the bulk of your time trying to figure out your customer's compliance and regulatory requirements.