---
layout: single
title:  "ARM Templates: Encrypt VMs with Azure Disk Encryption"
date:   2018-03-10 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

Azure Disk Encryption (ADE) allows you to encrypt the operating system and data drives of a virtual machine in Azure.  This allows you to encrypt both Windows and Linux virtual machines. 

### Prerequisites
The setup the foundations to encrypt virtual machines requires a few steps before you can start encrypting virtual machines.
1. Create Azure Active Directory Application
2. Create and configure Azure Key Vault
3. Allow the Azure Active Directory service principal to access the Key Vault

**Create Azure AD Application and Service Principal**

``` powershell
$AADappName = "kvEncryption"
$AADappURI = "http://$AADappName"
$AADappPW = "YourComplexPassw0rd"

$AADapp = New-AzureRmADApplication -DisplayName $AADappName -HomePage $AADappURI  -IdentifierUris $AADappURI -Password $AADappPW
$AADappId = $AADapp.ApplicationId

$AADappSPN = New-AzureRmADServicePrincipal -ApplicationId $AADappId
```

**Create and Configure Azure Keyvault**

``` powershell
#Create Key Vault
$kvName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location -ResourceGroupName $rgName -VaultName $kvName -EnabledForDiskEncryption

#Add Key Encryption Key (KEK) to the Key Vault
Add-AzureKeyVaultKey -VaultName $kvName -Name "myKEK" -Destination "Software"
```

**Allow the Azure Active Directory service principal to access the Keyvault**
``` powershell
#Add access for the Azure platform to access the Key Vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $kvName -ServicePrincipalName $app.ApplicationId -PermissionsToKeys "WrapKey" -PermissionsToSecrets "Set"
```
### Encrypt Virtual Machine
I created a quick template to deploy a new Windows Server 2016 image from the gallery and then launch the encryption process within the ARM template.

When supplying the parameters, you must remember all the values you used to setup the Azure Active Directory service principal and password and Key Vault name and Encryption Key URL.

If you don't know the Key Vault Encryption URL, you can run the following PowerShell commands

```powershell
$keyvaultName = ""
$rgName = ""
#Key Vault Encryption Key URL
(Get-AzureKeyVaultKey -VaultName $keyvaultName -Name myKey).Key.kid
```
ARM Template to deploy and encrypt virtual machine:
<script src="https://gist.github.com/erleonard/fd5c18a8a27305fecfd21b4d07344cc7.js"></script>