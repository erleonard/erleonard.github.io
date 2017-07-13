---
layout: single
title:  "Azure DevTest Labs Setup"
date:   2017-06-12 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - PowerShell
  - ARM
---
Recently I have been using DevTest Labs to test customer scenarios a lot and deploying a new lab is quite easy utilizing the [Azure DevTest Lab quickstart templates](https://github.com/Azure/azure-devtestlab).

Here are the steps to creating a DevTest Lab in your subscription:

## Create Resource Group
Create a new resource group in your Azure subscription.
<script src="https://gist.github.com/erleonard/636f95f9224ffc0025b23b7795ac9711.js"></script>

## Deploy DevTest Lab
Deploy DevTest lab to your Resource Group.  This creation of the lab via the ARM template or using the portal will automatically create you a Storage Account, Virtual Network, DevTest Lab and Key vault.

<script src="https://gist.github.com/erleonard/0e51352e2d3ca50fee2425cf2f5a4858.js"></script>

In under 2 minutes you can have a basic ready to go DevTest Lab. Next article I will cover how we can modify the ARM template to include additional features.