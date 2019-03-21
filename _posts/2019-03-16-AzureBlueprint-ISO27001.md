---
layout: single
title:  "Azure Blueprints: ISO27001 Shared Services"
date:   2019-03-16 14:17:12 -0500
toc: true
toc_icon: "cloud"
categories:
  - Azure
tags:
  - Azure
---

This post is part of a series. The previous posts in the series can be found here: 
 - [Azure Blueprints: Intro](http://erleonard.me/azure/AzureARMTemplates-SubstringFunction/)

Microsoft recently released sample blueprints to help you accelerate your configuration the foundation of Azure to help you meet your compliance needs. Part of the recent release was the sample blueprints for ISO 27001: Shared Services.

When configuring an enterprise environment that needs to meet compliance and regulatory needs, you will often deploy the hub and spoke model for IaaS and PaaS services. This is where the sample blueprint for ISO 27001: Shared Services fits as it gives you the starting blueprint to create your hub.

The blueprint is jam-packed with configurations to help you move things along.  

## ISO27001: Shared Services
Let's take a quick view as to what is included in the blueprint:

**Azure Security Center**
  - Configure Azure security center with the standard pricing tier.

**Log Analytics**
  - Create diagnostic storage account
  - Deploy Log Analytics workspace
  - Set default log data retention to 365 days

**Networking**
  - Deploy and configure Azure firewall
  - Deploy virtual network with IP range and subnets
  - Deploy network security group and application security groups
  - Set user-defined routes (UDR).

**Virtual Machines**
  - Deploy two active directory domain services servers
  - Deploy one jumpbox
  - Availability sets

**Policy**
  - Require blob encryption for storage accounts
  - Deploy Log Analytics Agent for Windows VMs
  - Deploy Threat Detection on SQL servers
  - Deploy SQL DB transparent data encryption
  - Allowed virtual machine SKUs
  - Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
  - Deploy Log Analytics Agent for Linux VMs
  - Deploy network watcher when virtual networks are created
  - Enforce encryption on Data Lake Store accounts
  - Allowed locations
  - Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
  - Enforce automatic OS upgrade with app health checks on VMSS
  - Allowed storage account SKUs
  - Deploy default Microsoft IaaSAntimalware extension for Windows Server
  - Allowed locations for resource groups

**Key Vault**
  - Deploy Azure key vault
  - Add secrets to key vault

As i said, this blueprint is packed with stuff and is pure gold for anybody who is looking into how to start there blueprint journey.

In the next steps, we will create the blueprint based on the ISO27001: Shared Services sample, make no modifications and just deploy it to a blank subscription.

## Create and Edit blueprint
1. Go to the [Azure Portal](https://portal.azure.com)
2. Select **All services** and search for blueprints.
3. Under **blueprint definitions**, click **+ create blueprint**.
4. On the choose a blueprint page, select **ISO 27001: Shared Services** sample blueprint.
5. On the create blueprint page, enter the following information:
  - Enter a **blueprint name** and make to only include letters, numbers or dashes.
  - Enter a description for the blueprint.
  - In **definition location**, select a management group or subscription.
6. Once you have provided a name, description and location for your blueprint, select **Save draft**.
7. After your blueprint has been saved right-click on your blueprint and select **Publish blueprint**.
8. The **Publish blueprint** blade will appear and provide a version number, change notes and click **Publish**. 

You are now ready to assign your blueprint to a management group or subscription.

## Assigned blueprint
To assign a blueprint follows almost the same steps a publishing blueprints.

1. Under **blueprint definitions**, right-click on the blueprint you have just created in the previous section, and select **assign blueprint**.
2. The **Assign blueprint** page will appear and enter the following information:
  - Assignment name: Enter a name
  - Location: Canada Central
  - Lock Assignment: select your lock preference.  Since this is demo purposed, the lock assignment is set to "don't lock".
  - Managed Identity: System Managed

After managed identity is where the blueprint parameters are for the individual items.  I will only cover the ones I changed and not cover the other items.
  - Organization name: set a short company name
  - Log Analytics template
    - Service tier: PerGB2018
    - Location: Canada Central
  - Virtual Network and Route Table template
    - Enable virtual network DDOS protection: false
  - Key Vault template
    - Jumobx admin ssh key or password: enter a password.
    - Domain admin password: enter a password.
    - AAD object ID: Get AAD object ID of the user that requires access to Key Vault.
  Jumpbox template
    - Key Vault resource id for jumpbox admin password: set to the following /subscriptions/**subscription id**/resourceGroups/**org name**-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/**org name**-sharedsvcs-kv
    - Key Vault secret name for jumpbox admin password: must match the jumpbox username found in the key vault template section.
  Active Directory Domain Services template
    - Key Vault resource id for domain admin password: set to the following /subscriptions/**subscription id**/resourceGroups/**org name**-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/**org name**-sharedsvcs-kv
    - Key Vault secret name for domain admin password: must match the domain username found in the key vault template section.
  - Select **Assign** and the blueprint will apply to the subscription you selected.

  Once the template gets assigned, it will take a little while for it to deploy and you can check the provisioning state or the activity logs for updates on the deployment.

## Lessons Learned
  When I originally deployed the template for the first time, it kept failing because I didn't read carefully the section for Key Vault. 
  
  The parameter "Key Vault secret name for domain(or jumpbox) admin password", I kept adding the password I wanted but this parameter field needed to match the domain username found in the key vault template section.

  Secondly, the blueprint failed on the first attempt when it is applying the DSC configuration for ADDS.  I had to re-assign the blueprint another time for it to be successful.

  Lastly, the Microsoft team really did a good job documenting the template at assignment time. If you are unsure of a parameter, hover the little information symbol for some useful information.  Remember to be like Eric and read!

## What's Missing
The sample template is fantastic but after the deployment, i feel like there are a few things that are missing:
1. There is no Azure Application Gateway deployed for ingress HTTP/S traffic.
2. Azure Backup is not deployed. All my conversations with people who take care of compliance, always ask about backups.

## What's Awesome
The ISO27001: Shared Services template is awesome but that's not what makes it awesome, it's the documentation. Microsoft included the control mapping documentation. You can find it here: [https://docs.microsoft.com/en-ca/azure/governance/blueprints/samples/iso27001-shared/control-mapping](https://docs.microsoft.com/en-ca/azure/governance/blueprints/samples/iso27001-shared/control-mapping)

In conclusion, I hope Microsoft continues to developping sample blueprints as I feel this can really help organizations accelerate their setup of there foundation of Azure.