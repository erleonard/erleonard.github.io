---
layout: single
title:  "Azure Provision VM - Part2"
date:   2017-04-14 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - PowerShell
---

Automate the creation of the resources required to deploy a single virtual machine using the Azure Resource Manager (ARM) PowerShell Cmdlets. This article will cover the steps to create a VM with managed disks.

If you would like to deploy a VM utilizing a storage account please see [Part 1](http://erleonard.me/azure/Azure-Provision-VM-Part1/).

## Create Resource Group
Create a new resource group in our Azure subscription.
<script src="https://gist.github.com/erleonard/636f95f9224ffc0025b23b7795ac9711.js"></script>

## Create Networking
Create Virtual Network, Public IP address, Network Security Group (NSG) and associated all to a new network interface.
<script src="https://gist.github.com/erleonard/948da813006b7b98534a649bc025dfd3.js"></script>

## Create and Deploy Virtual Machine
Create a new Windows Server 2016 Virtual Machine and deploy.
<script src="https://gist.github.com/erleonard/709e9eee048fc986a94167f200898238.js"></script>