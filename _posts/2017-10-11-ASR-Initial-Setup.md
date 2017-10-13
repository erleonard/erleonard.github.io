---
layout: single
title:  "Azure Site Recovery: Initial Setup"
date:   2017-10-11 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Azure Site Recovery
---

Continuing our series of blog posts for Azure Site Recovery, we will be creating the Recovery Services Vault utilizing an ARM Template.

To deploy this ARM template, you will need to define the resource group and location of that resource group from the script below. Tip: to get a list of locations for Azure you can use the following Azure command: Get-AzureRMLocation.

Once you have defined the variables, you can use the New-AzureRmResourceGroupDeployment command to deploy the template to your subscription.


## Deploy using PowerShell:
````powershell
#Login to Azure
Login-AzureRmAccount

$ResourceGroup = "RG-ASR"
$Location = "canadaeast"

#Create resource group
New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location

$parameters = @{}
$parameters.Add("VaultName", "ASRvault")
  New-AzureRmResourceGroupDeployment -Name ASRDeployment -ResourceGroupName $ResourceGroup -TemplateParameterObject $parameters -TemplateUri https://raw.githubusercontent.com/erleonard/AzureARMTemplates/master/Site-Recovery-Vault-Creation/azuredeploy.json'
  -verbose
````