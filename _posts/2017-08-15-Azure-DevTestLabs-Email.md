---
layout: single
title:  "Azure DevTest Labs deployment with Email Notifications"
date:   2017-08-15 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - ARM
---
My previous blog post on [scheduling DevTest Labs](http://erleonard.me/azure/Azure-DevTestLabs-Deployment-Schedules/) showed how you could use a paramater to fill the webhook URL for Auto-Shutdown notifications.  Since that blog post, you can now use an email address instead of a webhook.

### Shutdown Notifications
To add an email notification to your DevTest Lab, you need to change the property value of **webHookUrl** to NULL and add the **emailRecipient** property with the desired value.

<script src="https://gist.github.com/erleonard/a816e3f3bd0bd5e75f98e87369567c63.js"></script>

You can find the completed script in my GitHub repo: [https://github.com/erleonard/AzureARMTemplates/tree/master/CreateLabWithEmail](https://github.com/erleonard/AzureARMTemplates/tree/master/CreateLabWithEmail)