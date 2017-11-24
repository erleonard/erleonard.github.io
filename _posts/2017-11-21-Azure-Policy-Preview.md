---
layout: single
title:  "Azure Policy Public Preview"
date:   2017-11-21 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Azure Policy (public preview) is a new blade in the Azure portal that helps you manage policies easily across all of your subscriptions in a single place. The new blade provides continous monitoring of compliance from a single dashboard.

**Policy definition** <br>
Azure Policy already provides some pre-built definitions to help you get started.
The Policies that are available are:
- Preview: Apply Diagnostic Settings for Network Security Groups
- Preview: Audit missing blob encryption for storage accounts
- Preview: Automatic provisioning of security monitoring agent
- Preview: Deploy default Microsoft IaaSAntimalware extension for Windows Server
- Preview: Deploy network watcher when virtual networks are created
- Preview: Monitor missing Endpoint Protection in Security Center
- Preview: Monitor missing system updates in Security Center
- Preview: Monitor OS vulnerabilities in Security Center
- Preview: Monitor permissive network access in Security Center
- Preview: Monitor possible app Whitelisting in Security Center
- Preview: Monitor possible network JIT access in Security Center
- Preview: Monitor unaudited SQL database in Security Center
- Preview: Monitor unencrypted SQL database in Security Center
- Preview: Monitor unencrypted VM Disks in Security Center
- Preview: Monitor unprotected network endpoints in Security Center
- Preview: Monitor unprotected web application in Security Center
- Preview: Monitor VM Vulnerabilities in Security Center
- Allow resource creation if 'department' tag set
- Allow resource creation if 'environment' tag value in allowed values
- Allow resource creation only in Asia data centers
- Allow resource creation only in European data centers
- Allow resource creation only in India data centers
- Allow resource creation only in Japan data centers
- Allow resource creation only in United States data centers
- Allowed locations
- Allowed resource types
- Allowed storage account SKUs
- Allowed virtual machine SKUs
- Audit VMs that do not use managed disks
- Enforce automatic OS upgrade with app health checks on VMSS
- Enforce tag and its value
- Not allowed resource types
- Require blob encryption for storage accounts
- Require SQL Server version 12.0

With these policies you can assign a policy to a subscription and/or resource group. To create a policy simply select **Assignments** from the blade and click on **Assign Policy**. From there you can select the policy you want to apply and assign a scope for the policy and click on **Assign**.

**Azure Sample Policies GitHub repo** <br>
Microsoft has created a public repository for quick-start samples to be shared with the community.
https://github.com/Azure/azure-policy-samples