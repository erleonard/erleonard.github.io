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

Continuing our series of blog posts for Azure Site Recovery, we will be creating the Recovery Services Vault utilizing an ARM Template. Included in the template is a storage account to replicate your data to and virtual network to perform failovers.

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

New-AzureRmResourceGroupDeployment -Name ASRDeployment `
                                   -ResourceGroupName $ResourceGroup `
                                   -TemplateUri 'https://raw.githubusercontent.com/erleonard/AzureARMTemplates/master/Site-Recovery-Vault-Creation/azuredeploy.json' `
                                   -VaultName 'ASR' `
                                   -ASRVaultVNName 'ASRVault-VNET-PROD' `
                                   -ASRVaultSAType 'Standard_LRS' `
                                   -Verbose
````
## Deploy using Azure Portal
If you don't have PowerShell installed or don't want to use it, you can click on the button to deploy the template directly from the Azure Portal.

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Ferleonard%2FAzureARMTemplates%2Fmaster%2FSite-Recovery-Vault-Creation%2Fazuredeploy.json)