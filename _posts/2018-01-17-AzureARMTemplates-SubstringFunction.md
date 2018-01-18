---
layout: single
title:  "Azure Resource Manager templates: Substring Function"
date:   2018-01-17 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

Working with Functions in Azure ARM templates can be challenging at times. In my scenario, I'm building a single virtual network and the requirement is to create multiple sequential subnets for my deployment. My first task is to remove the zero from "subnet0" so that I can start incrementing the count.

To accomplish this, I need to use the substring function to set my starting character position and determine the length to capture. In this case, I want to capture the word "subnet", my starting character position is 0, and the length of the work is 6

Here is a sample ARM template that will output the result we need.


 ```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subnetName": {
            "type": "string",
            "defaultValue": "subnet0"
        },
        "name": {
            "type": "string",
            "defaultValue": "VNET"
        }
    },
    "variables": {
        "subnetModName": "[substring(parameters('subnetName'), 0, 6)]"
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('name'),'/',variables('subnetModName'))]",
            "type" : "string"
        }
    }
}
 ```

Result: **VNET/subnet**

The substring length is 6, and I'm not very happy with the result as the character length as it is set statically. I would like to ensure that if I use another name other than subnet0 that it will work as expected. To accomplish this, I need to determine the length of the word so that I can extract the substring and we can do this by using the length function.

This example will change the variable subnetModName to calculate the length of the word and capture the substring.


 ```json
  "subnetModName": "[substring(parameters('subnetName'), 0, length(parameters('subnetName'))]"
 ```

Result: **VNET/subnet0**

The outcome is not the desired result as the length was calculated to 7 and the substring function captured the entire word. My desired result is to remove the zero regardless if I use the string "subnet0" or "network0". To accomplish this, I need to calculate the length of the string, subtract the last character and capture this as a substring. Let's combine all these functions into one.

Back to our variable subnetModName, I modified the line of code to include the sub function and subtract the length by 1. This will calculate the length of subnet0 to 7 and subtract by 1 to equal 6. This will also work if I use a different string (ex: network0).


 ```json
  "subnetModName": "[substring(parameters('subnetName'),0,sub(length(parameters('subnetName')), 1))]"
 ```

 Result: **VNET/subnet**

The completed ARM template shows how you can use multiple string functions to achieve the desired result. We used the **length function** to count the number of characters wherein our string, used the **sub function** to subtract and finally use the **substring function** to capture our desired string.

 ```json
 {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subnetName": {
            "type": "string",
            "defaultValue": "subnet0"
        },
        "name": {
            "type": "string",
            "defaultValue": "VNET"
        }
    },
    "variables": {
        "subnetModName": "[substring(parameters('subnetName'),0,sub(length(parameters('subnetName')), 1))]"
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('name'),'/',variables('subnetModName'))]",
            "type" : "string"
        }
    }
}
```
In the next upcoming articles, we will continue to use our scenario to deploy a virtual network with multiple subnets and use that to build a dynamic ARM template for our deployment.

The code is also available on Github: [https://github.com/erleonard/AzureARMTemplates/blob/master/Functions/substring.json](https://github.com/erleonard/AzureARMTemplates/blob/master/Functions/substring.json)