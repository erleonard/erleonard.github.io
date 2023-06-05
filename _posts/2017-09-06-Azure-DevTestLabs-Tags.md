---
layout: single
title:  "Azure DevTest Labs deployment: Set Tags"
date:   2017-09-06 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - ARM
---

A new enhancement to Azure devtest labs now allows you to add tags to your lab. You can apply tags to VMs, NICs, IP addresses, load balancers, storage accounts and managed disks. You can apply tags during the deployment of your ARM template or manage them through the Tags blade under Configuration and settings.

Here is an example:

<script src="https://gist.github.com/erleonard/fddeee2a6e95df814ac113d03ec566b9.js"></script>

Not sure how to use tags, here are some great resources to get you started:

- [Use tags to organize your Azure resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags)
- [Prevent unexpected costs with Azure billing and cost management](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started)


Source:
[Azure DevTest Labs: Set tags on labs for improved cost tracking](https://azure.microsoft.com/en-gb/updates/sept-4-update-azure-devtest-labs-set-tags-on-labs-for-improved-cost-tracking/)