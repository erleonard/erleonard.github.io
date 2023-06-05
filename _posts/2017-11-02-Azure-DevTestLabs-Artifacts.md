---
layout: single
title:  "Azure DevTest Labs: Artifacts"
date:   2017-11-02 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Artifacts allow you to install applications after you provision your Virtual Machine.
In this article, I will discuss how you can use a storage account to install applications that may not be suitable to be published on the internet.

### Create File Share
1. From the Azure portal
2. Find and select the resource group that is associated with your lab.
3. Click on **Files**.
4. Click on **File Share**.
5. Provide the new file share with a **Name** and **Quota**. Click **Ok**.
6. Select the file share you just created and click on **Connect**.
7. Copy the powershell code into VScode and make note of the Username and Password.

### Add secrets to KeyVault
1. Go to your Lab and select **My secrets**
2. Under name type: SAname and for value add the username of the file share (ex:azure\DTLinstalls5597) and hit enter.
3. Under name type: SApassword and for the value add the password.
4. Create another secret for the Storage Account UNC file share.

### Create Artifacfile.json

User the following sample to create the Artifactfile.json to provide the username and password from the KeyVault.

````json
{
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "Sample App",
    "description": "Sample app for DTL demo ",
    "tags": [
        "Windows"
    ],
    "iconUri": "",
    "targetOsType": "Windows",
    "parameters": {
        "Username": {
            "type": "securestring",
            "displayName": "Username",
            "description": "Azure storage file share user account"
          },
          "Password": {
            "type": "securestring",
            "displayName": "Password",
            "description": "Azure storage file share user password"
          },
          "StorageAccount": {
            "type": "securestring",
            "displayName": "Storage Account Name",
            "description": "Azure storage account name"
          }
    },
    "runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./installSampleApp.ps1', ' -Username ', parameters('Username'), ' -Password ''', parameters('Password'), ''' -storageaccount ', parameters('storageaccount')']"
      }
}
````

### Create PowerShell script to install application
User the following sample to create the installSampleApp.ps1

````powershell
[CmdletBinding()]       
param
(
    [Parameter(Mandatory = $true)]
    [string] $Username,

    [Parameter(Mandatory = $true)]
    [string] $Password,

    [Parameter(Mandatory = $true)]
    [string] $SAaccount
)

$sampleAPP = "Z:\sample.exe"

try
{
    $acctKey = ConvertTo-SecureString -String "$password" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "$username", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "$SAaccount" -Credential $credential
}
catch
{
    Write-Error "Failed to map network drive to Azure Storage Account"
}

try
{
    Start-Process -FilePath $sampleAPP -ArgumentList "/SILENT"
}
catch
{
    Write-Error 'Failed to install application'
}
````
Once you have created the sample application, add it to your custom repository in DevTest Labs and test the installation of the artifact.