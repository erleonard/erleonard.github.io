---
layout: single
title:  "Azure Resource Manager Templates: Substring Function"
date:   2018-01-17 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

Azure ARM templates allow you to deploy your application and redeploy it at every stage of your application lifecycle. When you are deploying your application, you provide parameters to your deployment with your specific requirements and sometimes the deployment fails because it requires a unique name or other times you need your template to have some flexibility.

In your template, you can include Azure ARM template resource functions to add some flexibility to your JSON file. There are seven categories of resource functions: Array and object, Comparison, Deployment value, Logical, Numeric, Resource and String functions. 

We will be working with the string and numeric functions to manipulate the information provided into strings we desire. To accomplish our goal, we will be using: substring, length, concat, and sub.

- Substring: contains a subset of the string provided
- Length: calculates the length of the string
- Concat: Combines multiple string values into a single concatenated string
- Sub: Substracts two integers

Our scenario for the blog series, we need to deploy a single virtual network (vNet) and the requirement is to create multiple sequential subnets for the deployment. The first task is to remove the zero from “subnet0” so that I can start incrementing the count.

To accomplish this, we need to use the substring function to set my starting character position and determine the length to capture. In this case, we want to capture the word "subnet" and set the starting character position to 0, and the length of the word is 6.

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