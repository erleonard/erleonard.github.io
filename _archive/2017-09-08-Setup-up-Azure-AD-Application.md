---
layout: single
title:  "Setup up Azure AD Application for CommVault"
date:   2017-09-08 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - PowerShell
---

Working with CommVault v11, you can restore a VMware virtual machine to an Azure VM. To accomplish this, you need to add the Microsoft Azure Client in the CommCell console. But before you can add the client to the console, you need to perform some steps in Azure Active Directory.

In Azure Active Directory you need to register a new application and provide the application access to a specific resource group or the entire subscription.

I have created a PowerShell script to create the Azure AD application.

<script src="https://gist.github.com/erleonard/0cb53f726fcfb72f9caeb55d60433e9b.js"></script>

After you run the script, it will provide you with the subscription ID, Tenant ID, application ID and application password.

Once you have this information, you can configure the Azure Client in the CommVault CommCell console quickly and without issues.