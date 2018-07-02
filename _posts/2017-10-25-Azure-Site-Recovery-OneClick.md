---
layout: single
title:  "Azure Site Recovery One-Click Replication"
date:   2017-10-25 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Azure Site Recovery
---

Just announced, the Azure Site Recovery One-click Replication (public preview) is an easy to use feature that requires almost no time to setup.

All you have to do is go to your Virtual machine, under operations and click Disaster recovery (Preview). On the next screen you select your target location and click enable replication.

![ASR-OneClick]({{ site.url }}{{ site.baseurl }}/images/2017/2017-10-25-Azure-Site-Recovery-OneClick.PNG)

That's it, easy stuff.

Some limitation of the public preview I noticed is that you cannot modify the target settings (Resource Group, Virtual Network, Storage Account) but I'm sure those customization will be available at a later date.