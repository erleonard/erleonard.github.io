---
layout: single
title:  "Azure Virtual Network Service Endpoints (Preview)"
date:   2017-09-30 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

At Microsoft Ignite this week, it was revealed new functionality to Virtual Network to protect SQL and Storage accounts endpoints from the internet or restrict access to parts of your Virtual Network.

This feature is in preview currently and only available in certain regions:
* **Azure Storage**: WestCentralUS, WestUS2, EastUS, WestUS, AustraliaEast, and AustraliaSouthEast
* **Azure SQL Database**: WestCentralUS, WestUS2, and EastUS.

To enable service endpoints within your ARM templates, simply add the serviceEndpoints resource under Subnets

<script src="https://gist.github.com/erleonard/5c2d03e1af3a3fd7a558aed2e4fd76bb.js"></script>

Once your Virtual Network is ready, you can create/modify the storage account or SQL database to take advantage of this new feature.