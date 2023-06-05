---
layout: single
title:  "Azure DevTest Labs deployment with schedules"
date:   2017-06-12 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - ARM
---

This is a continuation of my last post on Deploying Azure DevTest Labs.

In this post, I will show you how you can make some modifications to the ARM template to schedule auto-startup, auto-shutdown of virtual machines and provide a Webhook URL to send notifications.

### Auto-Startup
The following examples shows how to Auto-Statup your lab VMs during weekdays at 6:00am. You can modify the template to add days, substract days or change the Auto-Startup time. Remember when inputing the time, it must be present in the 24 hour format.

<script src="https://gist.github.com/erleonard/2205b318a66842a130633be54c534f11.js"></script>

### Auto-Shutdown
To ensure we don't forget to shutdown our VMs, we can enable daily Auto-Shutdown at 7:00pm. This will ensure we keep our cost down.  You can modify the time if you wish to shutdown soon or later depending on your preference. Remember when inputing the time, it must be present in the 24 hour format.

<script src="https://gist.github.com/erleonard/4081214b0cf4241a0074179404067172.js"></script>

### Shutdown Notifications
Lastly, you can add a webhook to send shutdown notifications. 

<script src="https://gist.github.com/erleonard/2ecde35faec24a3b11fa033c39c08ad9.js"></script>

You can find the completed script in my GitHub repo: [https://github.com/erleonard/AzureARMTemplates/tree/master/CreateLab](https://github.com/erleonard/AzureARMTemplates/tree/master/CreateLab)