---
layout: single
title:  "Remove Subscription Content From Azure"
date:   2017-12-27 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - PowerShell
---

![Pacman]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-27-Pacman.png){:height="50%" width="50%"}

My friend Frank created this nifty little PowerShell script to delete all of the Resource Groups in an Azure subscription by selecting the right subscription name. Here is the original post: [http://www.frankysnotes.com/2016/12/need-to-nuke-azure-subscription.html](http://www.frankysnotes.com/2016/12/need-to-nuke-azure-subscription.html)

Frank’s script is fantastic and straightforward when you use your Azure subscription for demos or presentations and need to create and destroy frequently. I needed it to do a few things extra: I modified the script to provide the subscription name on execution and added a check to see if the resource group is locked. Resource locks are used to safeguard critical workloads that I am using and don't wish to delete.

````powershell
[CmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$true)]
        $SubscriptionName
    )

# Login
try
{
    Write-Host "Checking for active session" -ForegroundColor Cyan
    Get-AzureRmContext | Out-Null
}
catch [System.Management.Automation.PSInvalidOperationException]
{
    Write-Warning -Message 'No session detected. Prompting for login.'
    Login-AzureRmAccount
}
catch
{
    throw $_
}

Write-Host "You select the following subscription. (it will be display 15 sec.)" -ForegroundColor Cyan
Get-AzureRmSubscription -SubscriptionName $SubscriptionName | Select-AzureRmSubscription 

#Get all the resources groups, with the exception of the cloud shell
$allRG = Get-AzureRmResourceGroup | Where-Object ResourceGroupName -NotLike "*cloud-shell*"

#Gather all Resource Groups and check to see if there is a lock
$Results = foreach ($RG in $allRG) {
    $ResourceLocks = Get-AzureRmResourceLock -ResourceGroupName $RG.ResourceGroupName

    If ($ResourceLocks.count -eq 0) {
        $RGResourceLock = 'No'
    }
    else {
        $RGResourceLock = 'Yes'
    }

    [PSCustomObject]@{
        ResourceGroup = $RG.ResourceGroupName
        ResourceLock = $RGResourceLock
    }
}

$Results | FT -autosize

$lastValidation = Read-Host "Do you wich to delete ALL the resouces previously listed that are not locked? (YES/ NO)"

if($lastValidation.ToLower().contains("y")) {
    foreach ( $rg in $Results){

        If ($RG.ResourceLock -ne "Yes") {
            Write-Host "Deleting " $rg -ForegroundColor Cyan 
            Remove-AzureRmResourceGroup -Name $rg.ResourceGroup -Force -WhatIf
        }
    }
}else{
     Write-Host "Aborded. Nothing was deleted." -ForegroundColor Cyan
}
````

The code is also available on Github: [https://github.com/erleonard/AzurePowershell](https://github.com/erleonard/AzurePowershell)

Here is an example of me running the script in the Azure Cloud Shell.
![Script]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-27-Script-in-Action.gif)