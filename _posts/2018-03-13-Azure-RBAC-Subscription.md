---
layout: single
title:  "Azure Assign User to Subscription"
date:   2018-03-13 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Powershell
---

Once again today, I was asked about Role-Based Access Control (RBAC) in Azure and how to assign a new user to the subscription.

So, to give access to a new user in the portal, you simply need to follow these steps:
1. Go to portal.azure.com
2. Search for subscriptions and select the subscription you want to give access to.
3. On the left-hand side, click on **Access Control (IAM)**
4. Click **Add**
5. **Add permissions** blade will appear
6. Select the **role** you wish to assign and type in the **email address**. The user will be notified via email.


If you do this often, you can use PowerShell to assign the new user to the subscription.


<script src="https://gist.github.com/erleonard/5e624ebfa159033db42da5299bd6ebf9.js"></script>

Example:

    .\Set-AzureSubscriptionRBAC.ps1 -SubscriptionName VisualStudioEnterprise -userEmail email@address.com -role Contributor


Now to get this blog article bookmarked to some of my peeps.