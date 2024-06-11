---
layout: single
title:  "Azure Site Recovery and Azure Automation"
date:   2017-10-13 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Azure Site Recovery
---

Continuing on our series of blog posts for Azure Site Recovery we will be configuring the Automation Account and greater flexibilkity to our Recover Plan.

### Create Automation Account

1. In the Azure Portal, click on the "+" sign and search for **Automation**
2. Select **Automation** where the publisher is Microsoft.
3. Click Create
4. Enter the required information and ensure that "Create Azure Run As account" is set to yes.
5. Click Create

### Create sample runbook for email notifications
1. In the Azure portal, open your Automation account.
2. Under Process Automation, click **Runbooks**
3. Click **Add a runbook** and select **Create a new runbook**
4. Provide the runbook the name of ASR-EmailNotification and select PowerShell for runbook type. Click **Create**. 
6. Once the runbook is created, the editor will open automatically.
7. Add the following code, Under $MailParameters ensure that the **FROM**, **TO** and **Credential** fields are modified for your purpose. Click **Publish**.

````powershell
param ( 
    [Object]$RecoveryPlanContext
)

$MailParameters = @{
    From       = ''
    To         = ''
    SmtpServer = 'smtp.office365.com'
    Port       = '587'
    Credential = Get-AutomationPSCredential -Name "AutomationAccount"
    UseSsl     = $true
}

$RecoveryPlanName = $RecoveryPlanContext.RecoveryPlanName
$GroupID = $RecoveryPlanContext.GroupID

switch ($RecoveryPlanContext.FailoverType)
{
"Test" {
    $MailParameters.add("Subject","Test Failover for $RecoveryPlanName")
    $MailParameters.add("Body","$GroupID is now running.")
}
"Planned" {
    $MailParameters.add("Subject","Planned Failover for $RecoveryPlanName")
    $MailParameters.add("Body","$GroupID is now running.")
 }
"Unplanned" { 
    $MailParameters.add("Subject","Unplanned Failover for $RecoveryPlanName")
    $MailParameters.add("Body","$GroupID is now running.")
 }
default { Write-Output ("Runbook aborted because there no failover type was specified") }
}

Send-MailMessage @MailParameters
````
### Customize the Recovery Plan
1. Go to your Recovery Vault your created in the previous article.
2. Under **Manage**, click **Recovery Plans**
3. Click on your Recovery Plan and select **Customize**
4. Right-click **Group 1: Start**, and then select **Add pre-action**.
5. Enter name for the action, select the appropriate Automation Account and select ASR-EmailNotification runbook. Click Ok.

Once you have completed all the steps, you can perform a test failover and see your runbook in action.

### Other Resources
Microsoft also has some great sample runbooks to get you started.
[https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery)