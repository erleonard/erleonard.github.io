---
layout: single
title:  "PowerShell AzureRM Install Issue"
date:   2017-04-26 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---
I was working with the Azure ARM cmdlets today and encountered some issues with my deployments.  After some research, I found out that the AzureRM module I was using was outdated and needed an update. Installing a module is easy and painless but when I tried to install the updated version of the AzureRM module I encountered an error.

![screenshot_00]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-04-26-Install-AzureRM-Issue.png)

To resolve this issue, I needed to install the module with the AllowClobber option.

<script src="https://gist.github.com/erleonard/887c45e020e45b34705c04ba08ceffc3.js"></script>

Once I ran the above command, my issue was resolved.

![screenshot_01]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-04-26-Install-AzureRM-Resolved.png)