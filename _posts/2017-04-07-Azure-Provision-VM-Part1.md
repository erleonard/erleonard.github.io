---
layout: single
title:  "Azure Provision VM - Part1"
date:   2017-04-07 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - PowerShell
---
Automate the creation of the resources required to deploy a single virtual machine using the Azure Resource Manager (ARM) PowerShell Cmdlets. This article will cover the steps to create unmanged disks for our VM.  In part 2, we will create a VM with managed disks.

## Create Resource Group
Create a new resource group in our Azure subscription.
<script src="https://gist.github.com/erleonard/636f95f9224ffc0025b23b7795ac9711.js"></script>

## Create Storage Account
Create a new storage account to hold our VHDs.
<script src="https://gist.github.com/erleonard/6525708e2d7b75c0774cb345a0a06c97.js"></script>

## Create Networking
Create Virtual Network, Public IP address, Network Security Group (NSG) and associated all to a new network interface.
<script src="https://gist.github.com/erleonard/948da813006b7b98534a649bc025dfd3.js"></script>

## Create and Deploy Virtual Machine
Create a new Windows Server 2016 Virtual Machine and deploy.
<script src="https://gist.github.com/erleonard/5dc875f26148d9bd82a0becc42287938.js"></script>