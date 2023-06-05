---
layout: single
title:  "Azure Cloud Shell"
date:   2017-05-11 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

This week at Microsoft Build 2017, Azure Cloud Shell was unveiled as a new addition to the Azure portal. This is a built-in browser based Bash shell that allows developers to generate a full-fledged terminal environment and has a pre-configured set of tools for managing deployments. Each user will have persistent file storage in their Cloud Shell hosted in Microsoft Azure.

Cloud Shell sessions are not persistent and requires Azure file share to be mounted to persist the $Home directory.

Azure CLI 2.0 is integrated into the Cloud Shell and automatically authenticates all your available subscriptions. As well, it will be continuously updated to ensure you have the latest version.

PowerShell for the Cloud Shell is coming soon.

### First Time Running It
1. Launch Cloud Shell from the top menu bar in the Azure portal
![Screenshot0]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-05-11-Azure-Portal-Menu.PNG)
2. Select a subscription to create a storage account and Azure file share with
![Screenshot1]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-05-11-Azure-Cloud-Shell-Storage.PNG)
3. Select "Create storage"
4. After the initialization is complete, the Azure Cloud Shell will launch.
![Screenshot2]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-05-11-Azure-Cloud-Shell.png)

### Resources

- [Azure Cloud Shell Features](https://docs.microsoft.com/en-ca/azure/cloud-shell/features)
- [Azure Cloud Shell Quickstart](https://docs.microsoft.com/en-ca/azure/cloud-shell/quickstart)

