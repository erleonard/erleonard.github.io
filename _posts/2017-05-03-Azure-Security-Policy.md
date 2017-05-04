---
layout: single
title:  "Creating and Applying Azure Security Policies"
date:   2017-04-26 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Creating and applying Azure security policies can be accomplished pretty quickly utilizing Visual Studio (2015/2017/Code). 

### Create the Azure Security Policy Definition
1. Open Visual Studio and create and new JSON file
2. Once the file is open, provide the schema path to provide intellisense for editing the policy
    * http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json
3. Copy the snippet below to Visual Studio and save the file as DepartmentPolicyDefinition.json

<script src="https://gist.github.com/erleonard/195cbc99db7228f78c5d64f6fce7d586.js"></script>

Here is a quick video to show you the steps to create the JSON file in Visual Studio 2017:

<iframe width="560" height="315" src="https://www.youtube.com/embed/Nl_23SsgZoc" frameborder="0" allowfullscreen></iframe>

### Assign the Azure Security Policy


<script src="https://gist.github.com/erleonard/8ca9f247b601ec2b6bc3a47c74a5a9ac.js"></script>
