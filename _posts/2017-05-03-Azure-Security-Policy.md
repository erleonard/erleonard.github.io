---
layout: single
title:  "Creating and Applying Azure Security Policies"
date:   2017-04-26 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Creating and applying Azure security policies can be accomplished pretty quickly utilizing Visual Studio (2015/2017/Code). The following example will create an Azure Security Policy to block the creation of resources with a tag of Department. 

### Create the Azure Security Policy Definition
1. Open Visual Studio and create and new JSON file
2. Once the file is open, provide the schema path to provide intellisense for editing the policy
    * http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json
3. Copy the snippet below to Visual Studio and save the file as DepartmentPolicyDefinition.json

<script src="https://gist.github.com/erleonard/195cbc99db7228f78c5d64f6fce7d586.js"></script>

Here is a quick video to show you the steps to create the JSON file in Visual Studio 2017:

<iframe width="560" height="315" src="https://www.youtube.com/embed/Nl_23SsgZoc" frameborder="0" allowfullscreen></iframe>

### Assign the Azure Security Policy

Once the JSON file is created, we will switch to PowerShell to create the policy definition in Azure and assign it to our subscription.

First step is to login to our Azure Account:
<script src="https://gist.github.com/erleonard/818face93f059718f397f1ad55a9acc7.js"></script>

Afterwards we will need to select the appropriate subscription, create the azure security policy definition and then apply to our subscription.

<script src="https://gist.github.com/erleonard/8ca9f247b601ec2b6bc3a47c74a5a9ac.js"></script>

If you do not wish to make your policy subscription wide, you can apply it to a specific resource group.
<script src="https://gist.github.com/erleonard/db7c663be58e1d62598ec9ac1cf35973.js"></script>