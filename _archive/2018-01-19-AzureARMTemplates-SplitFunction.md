---
layout: single
title:  "Azure Resource Manager Templates: Split Function"
date:   2018-01-19 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

This post is part of a series. The previous posts in the series can be found here: 
 - [ARM Templates: Substring Function](http://erleonard.me/azure/AzureARMTemplates-SubstringFunction/)

In the previous blog post, we worked with the parameter subnetName to manipulate the string to remove the 0 at the end and creating a sequential count for each additional subnet. We used the following functions: substring, length, concat, and sub.

Continuing with our simple scenario, we are building a single virtual network and create multiple sequential subnets for the deployment. Our second task is to assign each new subnet a unique address range.

Here is a sample of a vNet that contains three equal subnets. To accomplish this is a template we would require a parameter for each subnet address prefix.

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

To give our template some flexibility, we can use the **split function** to extract the address prefix and get each octet. This will allow you to then increment the count on the third octet to create new subnets.

The example below will split the subnet address into an array and we will use that information to create a different subnet address of 10.2.1.0/24

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
Result: **Array with the values of: "10","2","0","0"**

The code is also available on Github: [https://github.com/erleonard/AzureARMTemplates/blob/master/Functions/split.json](https://github.com/erleonard/AzureARMTemplates/blob/master/Functions/split.json)

In the upcoming and final article, we will combine all that we have learned from using **functions** and create our deployment.