---
layout: single
title:  "Error with Export Activity Log to storage account"
date:   2019-02-15 14:17:12 -0500
toc: true
toc_icon: "cloud"
categories:
  - Azure
tags:
  - Azure
---

Activity log will write events that happen at the subscription level. There are 8 event categories: Administrative, Service Health, Resource Health, Alert, Autoscale, Recommendation, Security and Policy.

Depending on your security and compliance rules for your organization you may need to keep Azure activity logs longer then the default 90 day retention period. You can extend this retention using a variety of ways, but for my need I create a storage account and export the activity log and have a retention  period of 365 days or 1 year.

The process is quite easy to do.

## Export Activity Log
In the Azure portal go to Activity Log
Click the button Export to Event Hub
A new blade will appear
Ensure the correct subscription is selected
Selected the appropriate regions.
Select Export to a storage account
Select the appropriate storage account
Set the retention period
Click Save.

## The Error
Once I clicked saved, it immediately failed and got this message.
![ActivityLogError]({{ site.url }}{{ site.baseurl }}/assets/images/2019/2019-02-15-ActivityLogExportError.png)

I clicked on the message and clicked on the related events text to get more information on this error as I had no idea why this was happening.

After some time had passed, I check the resource providers and noticed that my monitoring provider (microsoft.insights) was not registered.
![ActivityLogError]({{ site.url }}{{ site.baseurl }}/assets/images/2019/2019-02-15-ResourceProvider.png)

I registered the provider and once it was completed and re-did the export activity log steps and it worked.

Not sure why the resource provider was not registered, the only thing I can come up with is that it was a new subscription.