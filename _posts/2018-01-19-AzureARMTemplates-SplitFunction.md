---
layout: single
title:  "Azure Resource Manager templates: Split Function"
date:   2018-01-19 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

This is a continuation of my previous article: INSERT

Continuing on our simple scenario, we building a single virtual network and the requirement is to create multiple sequential subnets for my deployment. Our second task is to assign each new subnet an address range.

Here is an sample of a vNet that contains three equal subnets. To accomplish this is a template we would require a parameter for each subnet address prefix.

```json
"subnets": [
    {
        "name": "subnet0",
        "properties": {
            "addressPrefix": "10.2.0.0/24"
        }
    },
    {
        "name": "subnet1",
        "properties": {
            "addressPrefix": "10.2.1.0/24"
        }
    },
    {
        "name": "subnet2",
        "properties": {
            "addressPrefix": "10.2.2.0/24"
        }
    }
]
```
To keep our template as dynamic as possible, we can use the **split function** to extract the address prefix and get each octet to then increment the count on the third octet to create our new subnets.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "addressPrefix": {
            "type": "string",
            "defaultValue": "10.2.0.0/24"
        }
    },
    "variables": {
        "SeperatedValues": "[split(parameters('addressPrefix'),'.')]"
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(variables('SeperatedValues')[0],'.',variables('SeperatedValues')[1],'.1.0/24')]",
            "type" : "string"
        }
    }
}
```